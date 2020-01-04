<template>
    <div>
        <!-- 按钮 -->
        <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        <!-- /按钮 -->
         <!-- 表格 -->
        <el-table :data="employees">
            <el-table-column  type="selection" prop="id" fixed="left" label="编号"></el-table-column>
            <el-table-column prop="name" label="产品名称"></el-table-column>
            <el-table-column prop="price"  label="价格"></el-table-column>
            <el-table-column prop="description" label="描述"></el-table-column>
            <el-table-column prop="status" label="所属产品"></el-table-column>
            <el-table-column fixed="right" label="操作">
            <template v-slot="slot">
                <!-- <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>  -->
                <!--  <a href="" @click.prevent="toUpateHandler(slot.row)">修改</a> --> 
                <i class="el-icon-edit" @click.prevent="toUpdateHandler(slot.row)"></i> 
                <i class="el-icon-delete" @click.prevent="toDeleteHandler(slot.row.id)"></i> 
                <a href="">详情</a>
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
  width="50%">
  <el-form :model="form" label-width="80px">
      <el-form-item label="产品名称">
          <el-input v-model="form.name"/>
      </el-form-item>
      <el-form-item label="价格">
          <el-input v-model="form.price"/>
      </el-form-item>
      <el-form-item label="所属栏目">
          <el-select v-model="form.categoryId">
              <el-option
      v-for="item in options"
      :key="item.value"
      :label="item.label"
      :value="item.value">
          </el-option>
          </el-select>
      </el-form-item>
      <el-form-item label="介绍">
          <el-input v-model="form.description"/>
      </el-form-item>
      <el-form-item label="产品主图">
  <el-upload
  class="upload-demo"
  action="https://jsonplaceholder.typicode.com/posts/"
  :on-preview="handlePreview"
  :on-remove="handleRemove"
  :before-remove="beforeRemove"
  multiple
  :limit="3"
  :on-exceed="handleExceed"
  :file-list="fileList">
  <el-button size="small" type="primary">点击上传</el-button>
  <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
</el-upload>
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
            title:"添加产品信息",
            visible:false,
        employees:[],
        form:{},
         options: [{
          value: '选项1',
          label: '洗护服务'
        }, {
          value: '选项2',
          label: '月嫂服务'
        }, {
          value: '选项3',
          label: '家具养护'
        }, {
          value: '选项4',
          label: '龙须面'
        }, {
          value: '选项5',
          label: '北京烤鸭'
        }],

        }
    },
    created(){
        //在页面加载出来的时候加载数据
        this.loadData();
    },
    methods :{
        submitHandler(){
            let url = "http://localhost:6677/product/saveOrUpdate";
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
          let url = "http://localhost:6677/product/findAll"
          request.get(url).then((response)=>{
              this.employees = response.data;
          })
        },
        toAddHandler(){
            this.title="添加产品信息";
            this.visible=true;
        },
         toDeleteHandler(id){
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
            }).then(() => {
            //调用后台接口，完成删除操作
            let url = "http://localhost:6677/product/deleteById?id="+id;
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
            this.title="修改产品信息";
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