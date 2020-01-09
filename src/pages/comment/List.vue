<template>
    <div>
    
    <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
    <el-button type="danger" size="small">批量删除</el-button>
    <el-table :data="comments">
      <el-table-column
      type="selection"
      width="55">
    </el-table-column>
        <el-table-column prop="id" label="编号"></el-table-column>
        <el-table-column prop="content" label="内容"></el-table-column>
        <el-table-column prop="commentTime" label="评论时间"></el-table-column>
        <el-table-column label="操作">
            <template v-slot="slot">
                <!-- <a href=""  @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                <a href="" @click.prevent="toUpdateHandler">修改</a> -->
                <i class="el-icon-delete" @click.prevent="toDeleteHandler(slot.row.id)"></i>
                <i class="el-icon-edit-outline" @click.prevent="toUpdateHandler(slot.row)"></i>
            </template>
        </el-table-column>
    </el-table>
    <!--分页开始-->
    <el-pagination
    layout="prev, pager, next"
    :total="50">
  </el-pagination>
  <!--分页结束-->
  <!--模态框-->
  <el-dialog
  :title="title"
  :visible.sync="visible"
  width="60%">
  <el-form :model="form" label-width="80px">
    <el-form-item label="内容">
      <el-input v-model="form.content"></el-input>
    </el-form-item>
    <el-form-item label="评论时间">
      <el-input v-model="form.commentTime"></el-input>
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
    //用于存放要向网页中显示的数据
    methods:{
      loadData(){
        //this是当前实例实例
        // VUE实例创建完毕
        let url ="http://localhost:6677/comment/findAll"
        request.get(url).then((response)=>{
          //将查询结果设置到comments中
          this.comments = response.data;
        })
      },
      submitHandler(){
            // 通过request与后台进行交互
            let url="http://localhost:6677/comment/saveOrUpdate";
            request({
                url,
                method:"POST",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)
            }).then((response)=>{
              //模态框关闭
              this.closeModalHandler();
              this.loadData();
              this.$message({
                type:"success",
                message:response.message
              })
            })
        },
        toAddHandler(){
          this.title="添加评论内容";
          this.form = {
            type:"comment"
          }
          this.visible=true;
        },
        closeModalHandler(){
          this.visible=false;
        },
        toUpdateHandler(row){
          this.title="修改评论内容";
          this.form=row;
          this.visible=true;
        },
        toDeleteHandler(id){
          this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          //调用后台接口，完成删除操作
          let url = "http://localhost:6677/comment/deleteById?id="+id;
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
      }
    },
    data(){
        
        return{
            visible:false,
            comments:[],
            form:{
              type:"comment"
            }
        } 
    },
    created (){
        this.loadData()
      }
}
</script>

<style scoped>
</style>