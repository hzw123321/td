<template>
  <div>
    <!-- 按钮 -->
    <el-button type="success" size="small" @click="toAddHandler">添加</el-button> 
    <el-button type="danger" size="small">批量删除</el-button>
    <!-- /按钮 -->
    <!-- 表格 -->
    <el-table :data="products">
      <el-table-column prop="id" label="编号"></el-table-column>
      <el-table-column width="200px" prop="name" label="产品名称"></el-table-column>
      <el-table-column prop="price" label="单价"></el-table-column>
      <el-table-column width="200px" prop="description" label="描述"></el-table-column>
      <el-table-column prop="categoryId" label="所属分类"></el-table-column>
      <el-table-column width="650px" prop="photo" label="照片"></el-table-column>
      <el-table-column label="操作" fixed="right">
        <template v-slot="slot">
          <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
          <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
        </template>
      </el-table-column>
    </el-table>
    <!-- /表格结束 -->
    <!-- 分页开始 -->
    <!-- <el-pagination layout="prev, pager, next" :total="50"></el-pagination> -->
    <!-- /分页结束 -->
    <!-- 模态框 -->
    <el-dialog
      :title="title"
      :visible.sync="visible"
      width="60%">

      <el-form :model="form" label-width="80px">
        <el-form-item label="产品名称">
          <el-input v-model="form.name"></el-input>
        </el-form-item>
        <el-form-item label="单价">
          <el-input v-model="form.price"></el-input>
        </el-form-item>
        <el-form-item label="所属栏目">
            <el-select v-model="form.categoryId">
                <el-option 
                    v-for="item in options" 
                    :key="item.id"
                    :label="item.name"
                    :value="item.id"></el-option>
            </el-select>
        </el-form-item>
        <el-form-item label="描述">
          <el-input type="textarea" v-model="form.description"></el-input>
        </el-form-item>

        <el-form-item label="图片">
          <el-upload
            class="upload-demo"
            action="http://134.175.154.93:6677/file/upload"
            :file-list="fileList"
            :on-success="uploadSuccessHandler"
            list-type="picture">
            <el-button size="small" type="primary">点击上传</el-button>
            <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
          </el-upload>
        </el-form-item>
      </el-form>

      <span slot="footer" class="dialog-footer">
        <el-button size="small" @click="closeModalHandler">取 消</el-button>
        <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
      </span>
    </el-dialog>
    <!-- /模态框 -->

  </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
  // 用于存放网页中需要调用的方法
  methods:{
    // 上传成功的事件处理函数
    uploadSuccessHandler(response){
      let photo = "http://134.175.154.93:8888/group1/"+response.data.id
      // 将图片地址设置到form中，便于一起提交给后台
      this.form.photo = photo;
    },
    // 加载栏目信息
    loadCategory(){
      let url = "http://localhost:6677/category/findAll"
      request.get(url).then((response)=>{
        // 将查询结果设置到products中，this指向外部函数的this
        this.options = response.data;
      })
    },
    loadData(){
      let url = "http://localhost:6677/product/findAll"
      request.get(url).then((response)=>{
        // 将查询结果设置到products中，this指向外部函数的this
        this.products = response.data;
      })
    },
    submitHandler(){
      //this.form 对象 ---字符串--> 后台 {type:'product',age:12}
      // json字符串 '{"type":"product","age":12}'
      // request.post(url,this.form)
      // 查询字符串 type=product&age=12
      // 通过request与后台进行交互，并且要携带参数
      let url = "http://localhost:6677/product/saveOrUpdate";
      request({
        url,
        method:"POST",
        headers:{
          "Content-Type":"application/x-www-form-urlencoded"
        },
        data:querystring.stringify(this.form)
      }).then((response)=>{
        // 模态框关闭
        this.closeModalHandler();
        // 刷新
        this.loadData();
        // 提示消息
        this.$message({
          type:"success",
          message:response.message
        })
      })
    },
    toDeleteHandler(id){
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        // 调用后台接口，完成删除操作
        let url = "http://localhost:6677/product/deleteById?id="+id;
        request.get(url).then((response)=>{
          //1. 刷新数据
          this.loadData();
          //2. 提示结果
          this.$message({
            type: 'success',
            message: response.message
          });
        })
        
        
      })
      
    },
    toUpdateHandler(row){
        this.title = "修改产品信息";
      // 模态框表单中显示出当前行的信息
      this.fileList = [];
      this.form = row;
      this.visible = true;
    },
    closeModalHandler(){
      this.visible = false;
    },
    toAddHandler(){
        this.title = "录入产品信息";
      this.fileList = [];
      // 将form变为初始值
      this.form = {}
      this.visible = true;
    }
  },
  // 用于存放要向网页中显示的数据
  data(){
    return {
        title:"录入产品信息",
        visible:false,
        products:[],
        options:[],
        form:{},
        fileList:[]
    }
  },
  created(){
    // this为当前vue实例对象
    // vue实例创建完毕 
    this.loadData();
    // 加载栏目信息，用于表单中下拉菜单
    this.loadCategory();
  }
}
</script>

<style scoped>
 
</style>