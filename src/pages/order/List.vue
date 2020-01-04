<template>
    <div>
       <el-tabs v-model="activeName" @tab-click="handleClick">
    <el-tab-pane label="所有订单" name="first">
        <el-table :data="order">
        <el-table-column prop="id" label="订单编号"></el-table-column>
        <el-table-column  prop="orderTime" label="下单时间"></el-table-column>
        <el-table-column prop="total" label="总价"></el-table-column>
        <el-table-column prop="status" label="状态"></el-table-column>
        <el-table-column prop="waiterId" label="顾客id"></el-table-column>
        <el-table-column label="操作">
        <template v-slot="slot">
            <a href="" @click.prevent="toDetailsHandler(slot.row)" class="el-icon-more"></a>
            
        </template>
        </el-table-column>
        
        </el-table>
        <el-dialog
            title="录入顾客信息"
            :visible.sync="visible"
            width="60%">
            ---{{form}}
            <el-form :model="form" label-width="80px">
                <el-form-item label="下单时间">
                    <el-input v-model="form.orderTime"></el-input>
                </el-form-item>
                <el-form-item label="总价">
                    <el-input v-model="form.total"></el-input>
                </el-form-item>
                <el-form-item label="状态">
                    <el-input v-model="form.status"></el-input>
                </el-form-item>
                <el-form-item label="顾客id">
                    <el-input v-model="form.waiterId"></el-input>
                </el-form-item>
            </el-form>
           
            <span slot="footer" class="dialog-footer">
                <el-button size="small" @click="closeModalHandler">取 消</el-button>
                <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
            </span>
        </el-dialog>
    </el-tab-pane>
    <el-tab-pane label="待支付" name="second">
        <el-table :data="order">
        <el-table-column prop="id" label="订单编号"></el-table-column>
        <el-table-column  prop="orderTime" label="下单时间"></el-table-column>
        <el-table-column prop="total" label="总价"></el-table-column>
        <el-table-column prop="status" label="状态"></el-table-column>
        <el-table-column prop="waiterId" label="顾客id"></el-table-column>
        <el-table-column label="操作">
        <template v-slot="slot">
            <a href="" @click.prevent="toDetailsHandler(slot.row)" class="el-icon-more"></a>
            
        </template>
        </el-table-column>
        
        </el-table>
        
    </el-tab-pane>
    <el-tab-pane label="待排单" name="third">
        <el-table :data="order">
        <el-table-column prop="id" label="订单编号"></el-table-column>
        <el-table-column  prop="orderTime" label="下单时间"></el-table-column>
        <el-table-column prop="total" label="总价"></el-table-column>
        <el-table-column prop="status" label="状态"></el-table-column>
        <el-table-column prop="waiterId" label="顾客id"></el-table-column>
        <el-table-column label="操作">
        <template v-slot="slot">
            <a href="" @click.prevent="toDetailsHandler(slot.row)" class="el-icon-more"></a>
            
        </template>
        </el-table-column>
        
        </el-table>
        <el-dialog
            title="录入顾客信息"
            :visible.sync="visible"
            width="60%">
           <el-radio v-model="radio" label="1">员工1</el-radio><br>
  <el-radio v-model="radio" label="2">员工2</el-radio>
           
            <span slot="footer" class="dialog-footer">
            
                <el-button size="small" type="primary" @click="paidan">派单</el-button>
            </span>
        </el-dialog>
    </el-tab-pane>
    <el-tab-pane label="待接单" name="fourth"></el-tab-pane>
    <el-tab-pane label="待服务" name="fourth"></el-tab-pane>
    <el-tab-pane label="待确认" name="fourth"></el-tab-pane>
    <el-tab-pane label="待完成" name="fourth"></el-tab-pane>
  </el-tabs>
    
 
    </div>
</template>
<script>




import request from '@/utils/request'
import querystring from 'querystring'
export default {
    methods:{
        paidan(){
this.visible =false;
        },
         submitHandler(){
                 let url = "http://localhost:6677/order/saveOrUpdate";
            request({
                url,
                method:"POST",
                headers:{
                         "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)
            }).then((response)=>{
                this.closeModelHandler();
                this.loadData();
                this.$message({
                    type:"succsee",
                    message:response.message
                })
            })
        },
        // submitHandler(){
        //         this.visible=false;
        // },
        closeModalHandler(){
            this.visible=false;
        },
         loaData(){
            let url ="http://localhost:6677/order/findAll"
        request.get(url).then((response)=>{
            this.order=response.data;
        })
        },
        toDetailsHandler(row){
         //打开模态框
           this.visible=true;
           //在模态框的表单中显示当前行的信息
           this.form=row;
        },
    
        
        
    },
    data(){
        return{
            radio: '1',
            radio: '2',
            visible:false,
            order:[],
            form:{
                type:"order"
            }
        }
    },
    created(){
        this.loaData();
    },
}





</script>

<style scoped>

</style>