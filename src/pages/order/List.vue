<template>
    <div>
      <!-- v-model 双向数据绑定 -->
        <el-tabs v-model="params.status" @tab-click="loadData">
      <el-tab-pane label="全部" name="全部"/>
      <el-tab-pane label="待派单" name="待派单" ></el-tab-pane>
      <el-tab-pane label="待接单" name="待接单" ></el-tab-pane>
      <el-tab-pane label="待服务" name="待服务" ></el-tab-pane>
      <el-tab-pane label="待确认" name="待确认" ></el-tab-pane>
      <el-tab-pane label="已完成" name="已完成" ></el-tab-pane>
    </el-tabs>
         <!-- 表格 -->
        <el-table :data="orders.list">
            <el-table-column prop="id" fixed="left" label="订单编号"></el-table-column>
            <el-table-column prop="orderTime" label="下单时间"></el-table-column>
            <el-table-column prop="total"  label="总价"></el-table-column>
            <el-table-column prop="status" label="状态"></el-table-column>
            <el-table-column prop="customerId" label="顾客id"></el-table-column>
            <el-table-column prop="addressId" label="地址id"></el-table-column>
            <el-table-column fixed="right" label="操作">
            <template v-slot="slot">
                <a href="javascript:void(0)">详情</a> 
                 <a href="" v-if="slot.row.status === '待派单'" @click.prevent="toSendOrderHandler(slot.row)">派单</a>

                <!-- <i class="el-icon-edit" @click.prevent="toUpdateHandler(slot.row)"></i> 
                <i class="el-icon-delete" @click.prevent="toDeleteHandler(slot.row.id)"></i>  -->
            </template>
        </el-table-column>
        </el-table>
        <!-- /表格 -->
         <!--分页-->
    <el-pagination
    layout="prev, pager, next"
    :total="orders.total" @current-change="pageChageHandler">
  </el-pagination>
        <!--/分页-->
  <el-dialog
      title="派单"
      :visible.sync="visible"
      width="60%"
    >
    <div>
      <p><strong>订单编号：{{form.id}}</strong></p>
      <p><strong>订单总价：{{form.total}}</strong></p>
      <p><strong>下单时间：{{form.orderTime}}</strong></p>
      <p><strong>服务员工：</strong>
  <el-radio-group v-model="waiterId">
    <el-radio border v-for="e in employees"
    :key="e.id"
    :label="e.id">{{e.realname}}</el-radio>
  </el-radio-group>
      </p>
    </div>
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
      form: {},
      params:{
          page:0,
          pageSize:10,
      },
      employees:[],
      waiterId:null
        }
    },
  
    created(){
        //在页面加载出来的时候加载数据
        this.loadData();
        this.loadEmployees();
    },
    methods :{
      //加载员工信息
      loadEmployees(){
        let url = "http://localhost:6677/waiter/findAll"
        request.get(url).then(response=>{
          this.employees = response.data;
        })
      },
        pageChageHandler(page){
            this.params.page = page-1;
            this.loadData();
        },
        //重载员工数据
    loadData(){
            //this ->vue实例，通过vue实例访问该实例中数据，methods中
            //this.title/this.toAddHandler
          let url = "http://localhost:6677/order/queryPage"
          if(this.params.status === "全部"){
            delete this.params.status;
          }
          request({
              url,
              method:"post",
              headers:{
                  "Content-Type":"application/x-www-form-urlencoded"
              },
              data:querystring.stringify(this.params)
          }).then((response)=>{
              this.orders = response.data;
          })
        },
        
        submitHandler(){
            let url = "http://localhost:6677/order/sendOrder";
            //前端向后台发送请求，完成数据的保存操作
            request({
                url,
                method:"GET",
                //告诉给后台我的请求体体中放的是查询字符串
                //请求体中的数据，将this.form转化为查询字符串发送给后台
                params:{
                  orderId:this.form.id,
                  waiterId:this.waiterId
                }
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
        //去派单，将模态框打开，然后选择员工作为派单对象
    toSendOrderHandler(row){
                this.title="选择员工派单";
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