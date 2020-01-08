<template>
    <div>
        栏目管理<br>
        <!-- 按钮 -->
        <el-button type="primary" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
    <!-- 表格 -->
     <el-table :data="categorys">
         <el-table-column prop="id" label="编号"></el-table-column>
         <el-table-column prop="name" label="栏目名称"></el-table-column>
         <el-table-column prop="num" label="序号"></el-table-column>
         <el-table-column prop="parentId" label="父栏目"></el-table-column>
         <el-table-column fixed="right" label="操作">
             <template v-slot="slot">
                 <a href="" @click.prevent="toDeleteHandler(slot.row.id )" class="el-icon-delete"></a>
                 <a href="" @click.prevent="toUpdateHandler(slot.row)" class="el-icon-edit"></a>
                  <a href="" @click.prevent="toDetailsHandler"  class="el-icon-more"></a> 

             </template> 
         </el-table-column>
     </el-table>
     <!-- 分页 -->
     <el-pagination 
     layout="prev,pager,next"
     :total="50">   
     </el-pagination>
     <!-- 模态框 -->
     <el-dialog
            :title="title"
            :visible.sync="visible"
            width="60%">
            测试：{{form}}
            <el-form label-width="80px" :model="form">
                
                <el-form-item label="栏目名称" >
                    <el-input  v-model="form.name">

                    </el-input>
                </el-form-item>
                <el-form-item label="序号" >
                    <el-input v-model="form.num">

                    </el-input>
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
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    //用于存放网页中需要调用的方法
    methods:{
        loadData(){
             let url ="http://localhost:6677/category/findAll"
        request.get(url).then((response)=>{
            //将查询结果设置到category中
            this.categorys=response.data;
        })
        },
    submitHandler(){
            //通过request与后台进行交互，并要携带参数
            let url="http://localhost:6677//category/saveOrUpdate"
            request({
                url,
                method:"POST",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                    
              data: querystring.stringify(this.form) 
            }).then((response)=>{
        // 模态框关闭
        this.closeModalHandler();
        // 刷新
        this.loadData();
        // 提示消息
        this.$message({
          type:"success",
          message:response.message
                })
            })
        },
       toAddHandler(){
            this.form={
                type:"column"
            }
            this.visible = true;
        },
       closeModalHandler(){
           this.visible=false;
       } ,
       toDetailsHandler(){
            this.title="查看产品信息"
            this.visible=true;
        },
       toDeleteHandler(id){
             this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
            //调用后台接口，完成删除操作
             let url = "http://localhost:6677//category/deleteById?id="+id;
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
            //打开模态框
           this.visible=true;
           //在模态框的表单中显示当前行的信息
           this.form=row;


       },
    },
    //data用户存放要向网页中显示的数据
    data(){
        return{
            visible:false,
            categorys:[],
            form:{
                type:"category"
            }
        }
    },
    created(){
        this.loadData();
        //this为当前vue实例对象
        // vue实例创建完毕
       
    }
}
</script>

<style scoped>

</style>
