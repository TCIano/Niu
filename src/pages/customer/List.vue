<template>
    <div>
        <!-- 按钮-->
        <el-button  type = "success" size="small" @click="toAddHandler">添加</el-button>
        <el-button  type = "danger" size="small">批量删除</el-button>
        <!-- 表格    冒号绑定数据    @绑定方法    访问变量+this--> 
        <el-table :data="customers">
            <el-table-column  prop = "id" label="编号"></el-table-column>  
            <el-table-column prop = "realname" label="姓名"></el-table-column>
            <el-table-column prop = "telephone" label="联系方式"></el-table-column>
            <el-table-column label="操作">
                <template v-slot = "slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)" >删除</a>

                    <!-- 当前行代码 -->
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
                </template>    
            </el-table-column> 
            
                
            
        </el-table>

        <!--分页开始  -->
        <el-pagination
        layout="prev, pager, next"
        :total="50">
        </el-pagination>

        <!-- 模态框-->
        
        <el-dialog :title="title" :visible.sync="visible"
                width="60%"
                >
                {{form}}
                <el-form :model="form" label-width="88px">
                    <el-form-item label="用户名">
                        <el-input v-model="form.username"></el-input>
                    </el-form-item>
                    <el-form-item label="密码">
                        <el-input type="password" v-model="form.password"></el-input>
                    </el-form-item>
                    <el-form-item label="真实姓名">
                        <el-input type="realname" v-model="form.realname"></el-input>
                    </el-form-item>
                    <el-form-item label="手机号">
                        <el-input type="telephone" v-model="form.telephone"></el-input>
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
//暴漏接口
export default {
    //用于存放网页中需要调用的方法
    methods:{
        loadData(){
             //vue实例创建完毕
        let url = "http://localhost:6677/customer/findAll"
        request.get(url).then((response)=>{
            //将查询结果设置到customers   this为当前vue实例  this只想外部函数this（created）
            
            this.customers = response.data
        })
        },
       //确定按钮
        submitHandler(){
            //this.form  对象  --- 字符串--> 后台{type :'customer',age:12}
            //通过requset与后台进行交互，且携带参数。
            let url = "http://localhost:6677/customer/saveOrUpdate"
            request({
                url,
                method:"POST",
                //告诉后台即将查询字符串，
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                //转换为查询字符串
                data:querystring.stringify(this.form)
            }).then((response)=>{
                //关闭模态框
                this.colseModul();
                // 刷新
                this.loadData();
                //提示消息
                this.$message({
                    type:"success",
                    message:response.message
                })
            })
        },
        // 删除 按钮
        toDeleteHandler(id){
            //确认
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          //调用后台接口完成删除操作
            let url  = "http://localhost:6677/customer/deleteById?id="+id;
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
        //更新按钮
        toUpdateHandler(row){

            //显示当前行的信息
            this.form = row;

            this.title = "修改顾客信息",
            this.visible = true;
        },
        // 弹框取消
        colseModul(){
            this.visible = false;
        },
        // 添加按钮i
        toAddHandler(){
            //将form变为初始值
            this.form = {
                type : "customer"
            },
            this.title  = "录入顾客信息",
            this.visible = true;
        }
    },
    //用于存放向网页中显示的数据
    data(){ 
        return{
        title:"录入顾客信息",
        visible:false,
        customers:[],
        form:{}
        }
    },
    created(){
       this.loadData();
       
    }
}
</script>



<style scoped>

    
</style>