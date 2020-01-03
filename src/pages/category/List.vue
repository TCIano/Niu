<template>
    <div>
        <font >栏目管理</font><br><br>
        <!-- 按钮 -->
        <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button> 
        <!-- 表格 -->
        <el-table :data="categories">
            <el-table-column label="编号" prop="id"></el-table-column>
            <el-table-column label="栏目名称" prop="name"></el-table-column>
            <el-table-column label="序号" prop="num"></el-table-column>
            <el-table-column label="父栏目" prop="parentId"></el-table-column>
            <el-table-column label="操作">
                <template v-slot = "slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">  </a>
                     <i class="el-icon-delete"></i>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">   </a>
                    <i class="el-icon-edit-outline"></i>
                </template>
            </el-table-column>
                
        </el-table>
        <!-- 分页框 -->
        <el-pagination
        layout="prev, pager, next"
        :total="50">
        </el-pagination>
        <!-- 模态框 -->
        <el-dialog :title="title" :visible.sync="visible" width="60%" >
            {{form}}
            <el-form :model="form" label-width="80px">
                <el-form-item label="栏目名称">
                    <el-input type="name" v-model="form.name"></el-input>
                 </el-form-item>
                <el-form-item label="序号">
                    <el-input type="num" v-model="form.num"></el-input>
                </el-form-item>
               
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="colseModul" size="small">取 消</el-button>
                <el-button type="primary" @click="submitHandler" size="small">确 定</el-button>
           </span>
        </el-dialog>


    </div>
</template>

<script>
import request from '@/utils/request' 
import querystring from 'querystring'
export default {
    data(){
        return{
            title:"录入栏目信息",
            visible:false,
            categories:[],
            form:{}
        }
    },
    methods:{

        created(){
       this.loadData();
       
    },

        colseModul(){
            this.visible = false;
        },

         //重载数据
        loadData(){
            let url = "http://localhost:6677/category/findAll"
            request.get(url).then((response)=>{
                //箭头函数中this 指向外部函数中的this
                this.categories = response.data;
            })
        },
        //删除
        toDeleteHandler(id){
             this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
            //调用后台接口完成删除操作
            let url  = "http://localhost:6677/category/deleteById?id="+id;
            request.get(url).then((response)=>{
                //刷新模态框
                this.loadData();
                //提示 结果 
                    this.$message({
                type: 'success',
                message: '删除成功!'
            });
            })

            })
         
        },
        //编辑
        toUpdateHandler(row){
             //显示当前行的信息
            this.form = row;
            this.title = "修改栏目信息"; 
            this.visible = true;

        },
        //添加按钮
        toAddHandler(){
            //将form变为初始值
            this.form = {
                type : "customer"
            },
            this.title = "录入员工信息";
            this.visible = true;
        },
        //添加目录里的取消按钮
        colseModul(){
            this.visible = false;
        },
        //添加目录里的确定按钮
        submitHandler(){
            let url = "http://localhost:6677/category/saveOrUpdate"
        //前端向后台发送请求，完成数据的保存操作
        request({
            url,
            method:'POST',
            //告知后台即将查询字符串,请求体
            headers:{
                 "Content-Type":"application/x-www-form-urlencoded"
            },
            //请求体中的数据，将this.form转换为查询字符串放送给后台
            data:querystring.stringify(this.form)
        }).then((response)=>{

            this.colseModul();
            this.loadData();
            this.$message({
                    type:"success",
                    message:response.message
                })
        })

        },

    }
    
}
</script>


<style scoped>

</style>

