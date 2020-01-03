<template>

    <div>

        <el-button type="success" size="small" @click="toAddHandler">添加</el-button>

        <el-button type="danger" size="small">全部删除</el-button>

    <el-table :data="customers">    

        <el-table-column fixed="left" prop="id" label="编号"></el-table-column>
       
        
        <el-table-column width="200" prop="realname" label="真实姓名"></el-table-column>
        
        <el-table-column prop="telephone" label="联系方式"></el-table-column>

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
  title="录入顾客信息"
  :visible.sync="visible"
  width="60%">
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
  <!-- :before-close="handleClose"> -->
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
    //用于存放网页中需要调用的方法
    methods:{
        loadData(){
            let url="http://localhost:6677/customer/findAll"
            request.get(url).then((response)=>{
                this.customers=response.data;
            })
        },
        submitHandel(){
            let url='http://localhost:6677/customer/saveOrUpdate'
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
              let url="http://localhost:6677/customer/deleteById?id="+id;
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
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        },
        toAddHandler(){
            this.form={
                type:"customer"
            }
            this.visible=true;
        }
    },
    //用于存放要想网页中显示的数据
    data(){
        return{
            visible:false,
            customers:[],
            form:{
                type:"customers"
            }
        }
    },
    //文档加载后调用
    created(){
        this.loadData()
        // let url="http://localhost:6677/swagger-ui.html"
        // request.get(url).then((response)=>{
        //     //将查询
        //     this.customers=response.data;
        // })
    }

}

</script>

<style scoped>



</style>