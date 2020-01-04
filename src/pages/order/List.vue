<template>
    <div>
        <!-- 按钮 -->
        <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        <!-- /按钮 -->
        <el-tabs v-model="activeName" @tab-click="handleClick">
      <el-tab-pane label="所有订单" name="first"/>
      <el-tab-pane label="待支付" name="second" ></el-tab-pane>
      <el-tab-pane label="待派单" name="third" ></el-tab-pane>
      <el-tab-pane label="待接单" name="fourth" ></el-tab-pane>
      <el-tab-pane label="待服务" name="fifth" ></el-tab-pane>
      <el-tab-pane label="待确认" name="sixth" ></el-tab-pane>
      <el-tab-pane label="已完成" name="seventh" ></el-tab-pane>
    </el-tabs>
         <!-- 表格 -->
        <el-table :data="orders">
            <el-table-column prop="id" fixed="left" label="订单编号"></el-table-column>
            <el-table-column prop="orderTime" label="下单时间"></el-table-column>
            <el-table-column prop="total"  label="总价"></el-table-column>
            <el-table-column prop="status" label="状态"></el-table-column>
            <el-table-column prop="customerId" label="顾客id"></el-table-column>
            <el-table-column prop="addressId" label="地址id"></el-table-column>
            <el-table-column fixed="right" label="操作">
            <template v-slot="slot">
                <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a> 
                <!-- <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>  -->
                <!-- <i class="el-icon-edit" @click.prevent="toUpdateHandler(slot.row)"></i> 
                <i class="el-icon-delete" @click.prevent="toDeleteHandler(slot.row.id)"></i>  -->
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
  <el-dialog
      title="录入订单信息"
      :visible.sync="visible"
      width="60%"
    >
      <el-form
        :model="form"
        label-width="80px">
        <!-- --{{ form }} -->
        <el-form-item label="订单编号">
          <el-input v-model="form.id" />
        </el-form-item>
        <el-form-item label="下单时间">
          <el-input v-model="form.orderTime" />
        </el-form-item>
        <el-form-item label="总价">
          <el-input v-model="form.total" />
        </el-form-item>
        <el-form-item label="状态">
          <el-input v-model="form.status" />
        </el-form-item>
        <el-form-item label="顾客id">
          <el-input v-model="form.customerId" />
        </el-form-item>
        <el-form-item label="地址id">
          <el-input v-model="form.addressId" />
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
           visible: false,
      orders: [],
      activeName: 'second',
      form: {
        type: 'order'
      }
        }
    },
    created(){
        //在页面加载出来的时候加载数据
        this.loadData();
    },
    methods :{
        handleClick(tab, event) {
      console.log(tab, event)
    },
        //重载员工数据
        loadData(){
            //this ->vue实例，通过vue实例访问该实例中数据，methods中
            //this。title/this.toAddHandler
          let url = "http://localhost:6677/order/findAll"
          request.get(url).then((response)=>{
              this.orders = response.data;
          })
        },
        submitHandler(){
            let url = "http://localhost:6677/order/save";
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
    toDeleteHandler(id) {
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        // 调用后台接口，完成删除操作
        // eslint-disable-next-line no-unused-vars
        const url = 'http://localhost:6677/order/deleteById?id=' + id
        request.get(url).then((response) => {
        // 1.刷新数据
          this.loadData()
          // 2. 提示结果
          this.$message({
            type: 'success',
            message: 'response.message'
          })
        })
      // 确认
      })
    },
    toAddHandler(){
                this.title="录入订单信息";
                this.visible=true;
    },
    toUpdateHandler(row){
                this.title="修改订单信息";
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