<template>
    <div>
        <el-button type="success" size="small" @click="toAddHandler">添加</el-button>

        <el-button type="danger" size="small">全部删除</el-button>
        <el-table :data="products">
            <el-table-column fixed="left" prop="id" label="编号"></el-table-column>
            <el-table-column fixed="left" prop="name" label="产品名称"></el-table-column>
            <el-table-column fixed="left" prop="price" label="价格"></el-table-column>
            <el-table-column fixed="left" prop="description" label="描述"></el-table-column> 
            <el-table-column fixed="left" prop="status" label="所属产品"></el-table-column>
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
                <el-form-item label="名称">
                    <el-input v-model="form.name"></el-input>
                </el-form-item>
                <el-form-item label="价格">
                    <el-input v-model="form.price"></el-input>
                </el-form-item>
                <el-dropdown>
                <span class="el-dropdown-link">
                    所属栏目<i class="el-icon-arrow-down el-icon--right"></i>
                </span>
                <el-dropdown-menu slot="dropdown">
                    <el-dropdown-item>黄金糕</el-dropdown-item>
                    <el-dropdown-item>狮子头</el-dropdown-item>
                    <el-dropdown-item>螺蛳粉</el-dropdown-item>
                    <el-dropdown-item disabled>双皮奶</el-dropdown-item>
                    <el-dropdown-item divided>蚵仔煎</el-dropdown-item>
                </el-dropdown-menu>
                </el-dropdown>
                <el-form-item label="介绍">
                    <el-input type="textarea" v-model="form.description"></el-input>
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
        submitHandel(){
             let url='http://localhost:6677/product/saveOrUpdate'
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
    loadData(){
             //this->vue实例 即data和method中的数据
             let url="http://localhost:6677/product/findAll"
            request.get(url).then((response)=>{
                //箭头函数中的this指向的是外部函数的this
                this.products=response.data;
            })
         },
          toUpdateHandler(row){
             this.form=row;
             this.title="修改产品信息"
             this.visible=true;
         },
         todDeleteHandler(id){
             this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
              let url="http://localhost:6677/product/deleteById?id="+id;
              request.get(url).then((response)=>{
                this.loadData();
            this.$message({
            type: 'success',
            message:response.message
          });
          })
        })
        },
         closeModalHandler(){
            this.visible=false;
        },
        toAddHandler(){
            this.form={
                type:"product"
            }
             this.title="添加产品信息"
            this.visible=true;
}},
    data(){
        return{
            title:"添加产品信息",
             visible:false,
            products:[],
            form:{
                type:"product"
            }
        }
},
created(){
        this.loadData()
        }
}
</script>
<style scoped>
    .el-dropdown-link {
    cursor: pointer;
    color: #409EFF;
  }
  .el-icon-arrow-down {
    font-size: 12px;
  }
</style>