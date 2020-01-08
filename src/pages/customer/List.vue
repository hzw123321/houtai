<template>
    <div>
        顾客管理<br>
        <el-button size="small" type="success" @click="toAddHandler">添加</el-button>
        <el-button size="small" type="danger">批量删除</el-button>
        <!-- 表格开始 -->
        <el-table :data="customers">
        <el-table-column prop="id" label="编号"></el-table-column>
        <el-table-column  prop="realname" label="姓名"></el-table-column>
        <el-table-column prop="telephone" label="联系方式"></el-table-column>
        <el-table-column label="操作">
        <template v-slot="slot">
            <a href="" @click.prevent="toDeleteHandler (slot.row.id )" class="el-icon-delete">删除</a>
            <a href="" @click.prevent="toUpdateMandler(slot.row)" class="el-icon-edit">修改</a>
        </template>
        </el-table-column>
        
        </el-table>
        <!-- 表格结束 -->
        <!-- 分页开始 -->
        <!-- <el-pagination
            layout="prev, pager, next"
            :total="50">
        </el-pagination> -->
        <!-- 分页结束 -->
        <!-- 模态框 -->
        <el-dialog
            title="录入顾客信息"
            :visible.sync="visible"
            width="60%">
            ---{{form}}
            <el-form :model="form" label-width="80px">
                <el-form-item label="用户名">
                    <el-input v-model="form.username"></el-input>
                </el-form-item>
                <el-form-item label="密码">
                    <el-input type="password" v-model="form.password"></el-input>
                </el-form-item>
                <el-form-item label="真实姓名">
                    <el-input v-model="form.realname"></el-input>
                </el-form-item>
                <el-form-item label="手机号">
                    <el-input v-model="form.telephone"></el-input>
                </el-form-item>
            </el-form>
           
            <span slot="footer" class="dialog-footer">
                <el-button size="small" @click="closeModalHandler">取 消</el-button>
                <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
            </span>
        </el-dialog>
        <!-- 模态框 -->
    </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    //用于存放网页中需要调用的方法
    methods:{
        loadData(){
             let url ="http://localhost:6677/customer/findAll"
        request.get(url).then((response)=>{
            //将查询结果设置到customers中
            this.customers=response.data;
        })
        },
        submitHandler(){
            //通过request与后台进行交互，并要携带参数
            let url="http://localhost:6677/customer/saveOrUpdate"
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
                type:"customer"
            }
            this.visible = true;
        },
       closeModalHandler(){
           this.visible=false;
       } ,
       toDeleteHandler(id){
             this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
            //调用后台接口，完成删除操作
             let url = "http://localhost:6677/customer/deleteById?id="+id;
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
       toUpdateMandler(row){
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
            customers:[],
            form:{
                type:"customer"
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
