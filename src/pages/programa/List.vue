<template>
    <div>
        <el-button type="success" size="small" @click="toAddHandler">添加</el-button>

        <el-button type="danger" size="small">全部删除</el-button>
        <el-table :data="products">
            <el-table-column fixed="left" prop="id" label="编号"></el-table-column>
            <el-table-column prop="name" label="栏目名称"></el-table-column>
            <el-table-column prop="num" label="序号"></el-table-column>
        <el-table-column label="操作">
            <template v-slot="slot">
                    <a href="" @click.prevent="todDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
            </template>
        </el-table-column>
        </el-table>
        <el-pagination
            layout="prev, pager, next" :total="50">
        </el-pagination>
        <el-dialog 
            :title="title"
            :visible.sync="visible"
             width="60%">
            <el-form :model="form" label-width="80px">
            <el-form-item label="栏目名称">
            <el-input v-model="form.name"></el-input>
            </el-form-item>
            <el-form-item label="序号">
            <el-input v-model="form.num"></el-input>
            </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button size="small" @click="closeModalHandler">取 消</el-button>
                <el-button size="small" type="primary" @click="submitHandel">确 定</el-button>
            </span>
        </el-dialog>
    </div>
</template>
<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    methods:{
        loadData(){
            let url="http://localhost:6677/category/findAll"
            request.get(url).then((response)=>{
                this.products=response.data;
            })
        },
        submitHandel(){
            let url='http://localhost:6677/category/saveOrUpdate'
            request({
                url,
                method:"POST",
                header:{
                    "Comtent-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)
            }).then((response)=>{
                //模块框关闭
                this.closeModalHandler();
                //刷新
                this.loadData();
                //提示消息
                this.$message({
                    type:"success",
                    message:response.message
                });
            })
        },
        todDeleteHandler(id){
             this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
              let url="http://localhost:6677/category/deleteById?id="+id;
              request.get(url).then((response)=>{
                this.loadData();
            this.$message({
            type: 'success',
            message:response.message
          });
          })
        })
        },
        toUpdateHandler(row){
            this.form=row;
            this.title="修改栏目信息"
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        },
        toAddHandler(){
            this.form={
                type:"category"
            }
            this.title="添加栏目信息"
            this.visible=true;
        }
    },
    data(){
        return{
            visible:false,
            title:"添加栏目信息",
            products:[],
            form:{
                type:"category"
            }
        }
    },
    created(){
        this.loadData()
    }
}
</script>
<style scoped>

</style>