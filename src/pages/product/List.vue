<template>
    <div>
        产品管理<br>
        <!-- 按钮 -->
        <el-button type="primary" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
    <!-- 表格 -->
        <el-table :data="products">
        <el-table-column fixed="left" prop="id" label="编号"></el-table-column>
         <el-table-column width="100px" prop="name" label="参品名称"></el-table-column>
         <el-table-column prop="price" label="价格"></el-table-column>
         <el-table-column width="200px" prop="description" label="描述"></el-table-column>
         <el-table-column prop="categoryId" label="所属栏目"></el-table-column>
         <el-table-column width="650px" prop="photo" label="照片"></el-table-column>
         <el-table-column fixed="right" label="操作" >
             <template v-slot="slot">
                <a href="" @click.prevent="toDeleteHandler(slot.row.id)"  class="el-icon-delete"></a>
                <a href="" @click.prevent="toUpdateHandler(slot.row)"  class="el-icon-edit"></a> 
                <a href="" @click.prevent="toDetailsHandler"  class="el-icon-more"></a> 

             </template> 
         </el-table-column>
     </el-table>
     <!-- 分页 -->
     <el-pagination
            layout="prev, pager, next"
            :total="50">
        </el-pagination>
     <!-- 模态框 -->
      <el-dialog
            :title="title"
            :visible.sync="visible"
            width="60%">
     测试:{{form}}
        <el-form label-width="80px" :model="form">
         <el-form-item label="产品名称">
             <el-input v-model="form.name"></el-input>
         </el-form-item>
         <el-form-item label="价格">
             <el-input v-model="form.price"></el-input>
         </el-form-item>
         <el-form-item label="描述">
             <el-input type="textarea" v-model="form.description"></el-input>
         </el-form-item>
         <el-form-item label="所属栏目">
              <el-select v-model="form.categoryId" clearable placeholder="所属栏目">
                <el-option
                v-for="item in options"
                :key="item.id"
                :label="item.name"
                :value="item.id">
                </el-option>
               </el-select>
         </el-form-item>
        <el-form-item label="图片">           
                <el-upload
                    class="upload-demo"
                    action="http://134.175.154.93:6677/file/upload"
                    :file-list="fileList"
                    :on-success="uploadSuccessHandler"
                    list-type="picture">
                    <el-button size="small" type="primary">点击上传</el-button>
                    <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
                    </el-upload>
        </el-form-item>
     </el-form>
         <span slot="footer" class="dialog-footer">
                <el-button size="small" @click="closeModalHandler">取 消</el-button>
                <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
            </span>
     </el-dialog>
    </div>
     
</template>
<script>
import request from '@/utils/request'//自定义库
import querystring from 'querystring'//系统库
export default {
    data(){
        return {
            
            options: [],
            title:"录入产品信息",
            visible:false,
            products:[],
            fileList:[],
            form:{
            }
        }

    },
    created(){
        //在页面加载出来的时候加载数据
        this.loadData();
        this.loadCategory();

    },
    methods:{
        //上传成功的事件处理函数
        uploadSuccessHandler(response){
            let photo="http://134.175.154.93:8888/group1/"+response.data.id
            //将图片地址设置到form中 ，便于一起提交后台
            this.form.photo=photo;
            console.log(response);
        },
        loadCategory(){
            let url ="http://localhost:6677/category/findAll"
            request .get(url).then((response)=>{
                //箭头函数中的this指向外部函数中的this
                this.options=response.data;
            })

        },
         loadData(){
            let url ="http://localhost:6677/product/findAll"
            request .get(url).then((response)=>{
                //箭头函数中的this指向外部函数中的this
                this.products=response.data;
            })

        },
        toDetailsHandler(){
            this.title="查看产品信息"
            this.visible=true;
        },
        toAddHandler(row){
            this.fileList=[];
            this.title="录入产品信息"
            this.visible=true;
            this.form={}
        },
        submitHandler(){
            let url="http://localhost:6677/product/saveOrUpdate"
            //前端向后台发送请求，完成数据的保存操作
           // this.closeModalHandler();
                request({
                    url,
                    method:"POST",
                    headers:{
                        "Content-Type":"application/x-www-form-urlencoded"
                    },
                     data: querystring.stringify(this.form)
                }).then((response)=>{
                    this.closeModalHandler();
                    this.loadData();
                    this.$message({type:"success",message:response.message})
                })
            
        },
        closeModalHandler(){
            this.visible=false;
        },
        toDeleteHandler(id){
             this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
            //调用后台接口，完成删除操作
             let url = "http://localhost:6677/product/deleteById?id="+id;
            request.get(url).then((response)=>{
                //刷新数据
                this.loadData();
                //提示结果
                    this.$message({
                type: 'success',
                message: response.message
            });
            })
            })
         
       },
        toUpdateHandler(row){
            this.fileList=[];
            this.form=row;
            this.visible=true;

        },

    }

}
</script>
<style scoped>

</style>