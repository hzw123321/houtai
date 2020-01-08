<template>
    <div>
        员工管理<br>
    <el-button size="small" type="primary" @click="toAddHanler"> 添加</el-button>
    <el-button size="small" type="danger"> 删除</el-button>
        <el-table :data="employee">
         <el-table-column label="编号" prop="id"></el-table-column>
        <el-table-column label="姓名" prop="realname"></el-table-column>
        <el-table-column label="性别" prop="gender"></el-table-column>
        <el-table-column label="手机号" prop="telephone"></el-table-column>
        <el-table-column width = "200" label="身份证号" prop="idCard"></el-table-column>
        <el-table-column width = "200" label="银行卡号" prop="bankCard"></el-table-column>
        <el-table-column label="操作">
            <template v-slot="slot">
            
            <a href="" @click.prevent="toUpdateHandler(slot.row)" class = "el-icon-edit"></a>
            <a href="" @click.prevent="toDeleteHandler(slot.row.id)" class="el-icon-delete"></a>
        </template>
        </el-table-column>
        </el-table>
        <el-pagination
            layout="prev, pager, next" :total="50">
        </el-pagination>
        <el-dialog :title="title" :visible.sync="visible" width="60%">
            ---{{form}}
            <el-form :model="form">
                <el-form-item label="用户名" fixed = "letf">
                <el-input v-model="form.username"></el-input>
                </el-form-item>

                <el-form-item label="密码" fixed = "letf">
                <el-input type="password" v-model="form.password"></el-input>
                </el-form-item>

                <el-form-item label="姓名" >
                    <el-input v-model="form.realname"></el-input>
                </el-form-item>

                <el-form-item label="性别" >
                    <el-radio-group v-model="form.gender">
                         <el-radio label="3">男</el-radio>
                         <el-radio label="6">女</el-radio>
                    </el-radio-group>
                </el-form-item>

                <el-form-item label="手机号" >
                    <el-input  v-model="form.telephone"></el-input>
                </el-form-item>
                
                <el-form-item label="身份证号">
                    <el-input v-model="form.idCard"></el-input>
                </el-form-item>
               
                <el-form-item label="银行卡号">
                    <el-input v-model="form.bankCard"></el-input>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button size="small" @click="visible = false">取 消</el-button>
                <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
            </span>
        </el-dialog>
    </div>
</template>

<script>
import querystring from 'querystring'
import request from'@/utils/request'
export default {
    data(){
        return{
            form:{
                type:'waiter'
            },
        title:"录入员工信息",
            visible:false,
            employee:[]
        }
    },
    methods:{
        submitHandler(){
            let url = "http://localhost:6677/waiter/saveOrUpdate";
            request({
                url,
                method:"POST",
                headers:{
                         "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)
            }).then((response)=>{
                this.closeModalHandler();
                this.loadData();
                this.$message({
                    type:"success",
                    message:response.message
                })
            })
        },
        closeModalHandler(){
            this.visible = false;
        },
        //重载员工数据
        loadData(){
            let url = "http://localhost:6677/waiter/findAll"
      request.get(url).then((response)=>{
        // 将查询结果设置到customers中，this指向外部函数的this
        this.employee = response.data;
      })
        },
        toAddHanler(){
        this.form = {
            type: "employee"
        }
         this.visible = true;
        },
        toDeleteHandler(id){
          this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
            let url = "http://127.0.0.1:6677/waiter/deleteById?id="+id;
                request.get(url).then((response)=>{
                    //刷新数据,提示结果
                    this.loadData();
                        this.$message({
                        type: 'success',
                        message: response.message
             });
                })
                    
          });
        

    },
        toUpdateHandler(row){
        this.form = row;
        this.visible=true
    }
        
    },
        created(){
            this.loadData();
    }

    
}
</script>

<style scoped>

</style>