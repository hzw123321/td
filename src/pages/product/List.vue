<template>
    <div>
        产品管理<br>
        <!-- 按钮 -->
        <el-button type="primary" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
    <!-- 表格 -->
        <el-table :data="products">
        <el-table-column fixed="left" prop="id" label="编号"></el-table-column>
         <el-table-column prop="name" label="参品名称"></el-table-column>
         <el-table-column prop="price" label="价格"></el-table-column>
         <el-table-column prop="description" label="描述"></el-table-column>
         <el-table-column prop="status" label="所属栏目"></el-table-column>
         <el-table-column fixed="right" label="操作">
             <template v-slot="slot">
                <a href="" @click.prevent="toDeleteHandler(slot.row.id)"  class="el-icon-delete"></a>
                <a href="" @click.prevent="toUpdateHandler(slot.row)"  class="el-icon-edit"></a> 
                <a href="" @click.prevent="toDetailsHandler"  class="el-icon-more"></a> 

             </template> 
         </el-table-column>
     </el-table>
     <!-- 分页 -->
     <el-pagination
            layout="prev, pager, next"
            :total="50">
        </el-pagination>
     <!-- 模态框 -->
      <el-dialog
            :title="title"
            :visible.sync="visible"
            width="60%">
     测试:{{form}}
        <el-form label-width="80px" :model="form">
        
         <el-form-item label="产品名称">
             <el-input v-model="form.name"></el-input>
         </el-form-item>
         <el-form-item label="价格">
             <el-input v-model="form.price"></el-input>
         </el-form-item>
         <el-form-item label="描述">
             <el-input v-model="form.description"></el-input>
         </el-form-item>
         <el-form-item label="所属栏目">
              <el-select v-model="value" clearable placeholder="所属栏目">
    <el-option
      v-for="item in options"
      :key="item.value"
      :label="item.label"
      :value="item.value">
    </el-option>
  </el-select>
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
import request from '@/utils/request'//自定义库
import querystring from 'querystring'//系统库
export default {
    data(){
        return {
            
            options: [{
          value: '选项1',
          label: '黄金糕'
        }, {
          value: '选项2',
          label: '双皮奶'
        }, {
          value: '选项3',
          label: '蚵仔煎'
        }, {
          value: '选项4',
          label: '龙须面'
        }, {
          value: '选项5',
          label: '北京烤鸭'
        }],
            title:"录入产品信息",
            visible:false,
            products:[],
            form:{
                type:"product"
            }
        }

    },
    created(){
        //在页面加载出来的时候加载数据
        this.loadData();

    },
    methods:{
         loadData(){
            let url ="http://localhost:6677/product/findAll"
            request .get(url).then((responsse)=>{
                //箭头函数中的this指向外部函数中的this
                this.products=responsse.data;
            })

        },
        toDetailsHandler(){
            this.title="查看产品信息"
            this.visible=true;
        },
        toAddHandler(row){
            this.title="录入产品信息"
            this.visible=true;
        },
        submitHandler(){
            let url="http://localhost:6677/product/saveOrUpdate"
            //前端向后台发送请求，完成数据的保存操作
           // this.closeModalHandler();
                request({
                    url,
                    method:"POST",
                    headers:{
                        "Content-Type":"application/x-www-form-urlencoded"
                    },
                     data: querystring.stringify(this.form)
                }).then((response)=>{
                    this.closeModalHandler();
                    this.loadData();
                    this.$message({type:"success",message:response.message})
                })
            
        },
        closeModalHandler(){
            this.visible=false;
        },
        toDeleteHandler(id){
             this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
            //调用后台接口，完成删除操作
             let url = "http://localhost:6677/product/deleteById?id="+id;
            request.get(url).then((response)=>{
                //刷新数据
                this.loadData();
                //提示结果
                    this.$message({
                type: 'success',
                message: response.message
            });
            })
            })
         
       },
        toUpdateHandler(row){
            this.form=row;
            this.visible=true;

        },

    }

}
</script>
<style scoped>

</style>