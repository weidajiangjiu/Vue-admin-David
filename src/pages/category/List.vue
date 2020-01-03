<template>
    <div>
        <!-- 按钮 -->
        <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        <!-- /按钮 -->
         <!-- 表格 -->
        <el-table :data="employees">
            <el-table-column  type="selection" prop="id" fixed="left" label="编号"></el-table-column>
            <el-table-column prop="name" label="栏目名称"></el-table-column>
            <el-table-column prop="num"  label="序号"></el-table-column>
            <el-table-column prop="parentId" label="父栏目"></el-table-column>
            <el-table-column fixed="right" label="操作">
            <template v-slot="slot">
                <!-- <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>  -->
                <!--  <a href="" @click.prevent="toUpateHandler(slot.row)">修改</a> --> 
                <i class="el-icon-edit" @click.prevent="toUpdateHandler(slot.row)"></i> 
                <i class="el-icon-delete" @click.prevent="toDeleteHandler(slot.row.id)"></i> 
            </template>
        </el-table-column>
        </el-table>
        <!-- /表格 -->
         <!--分页-->
    <el-pagination
    layout="prev, pager, next"
    :total="50">
  </el-pagination>
  <!--/分页-->
   <!--模态框-->
  <el-dialog
  :title="title"
  :visible.sync="visible"
  width="60%">
  <el-form :model="form" label-width="80px">
      <el-form-item label="栏目名称">
          <el-input v-model="form.name"/>
      </el-form-item>
      <el-form-item label="序号">
          <el-input v-model="form.num"/>
      </el-form-item>
  </el-form>
  <span slot="footer" class="dialog-footer">
    <el-button @click="closeModalHandler">取 消</el-button>
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
        return{
            title:"添加栏目信息",
            visible:false,
        employees:[],
        form:{
            type:"category"
        }
        }
    },
    created(){
        //在页面加载出来的时候加载数据
        this.loadData();
    },
    methods :{
        submitHandler(){
            let url = "http://localhost:6677/category/saveOrUpdate";
            //前端向后台发送请求，完成数据的保存操作
            request({
                url,
                method:"POST",
                //告诉给后台我的请求体体中放的是查询字符串
                 headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                //请求体中的数据，将this.form转化为查询字符串发送给后台
                data:querystring.stringify(this.form)
            }).then((response)=>{
                //模态框关闭
                this.closeModalHandler();
                //刷新
                this.loadData();
                //提示消息
                this.$message({
                    type:"success",
                    message:response.message
                })
            })
        },
        //重载员工数据
        loadData(){
            //this ->vue实例，通过vue实例访问该实例中数据，methods中
            //this。title/this.toAddHandler
          let url = "http://localhost:6677/category/findAll"
          request.get(url).then((response)=>{
              this.employees = response.data;
          })
        },
        toAddHandler(){
            this.title="添加栏目信息";
            this.visible=true;
        },
        toDeleteHandler(id){
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
            }).then(() => {
            //调用后台接口，完成删除操作
            let url = "http://localhost:6677/category/deleteById?id="+id;
            request.get(url).then((request)=>{
                //刷新数据
                this.loadData();
                //提示消息
            this.$message({
                type: 'success',
                message: '删除成功!'
            });
            });
        }).catch(() => {
            this.$message({
                type: 'info',
                message: '已取消删除'
            });          
            });
        },
        toUpdateHandler(row){
            this.title="修改栏目信息";
            this.form = row;
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        }
    }
}
 </script>

 <style scoped>
 
 </style>