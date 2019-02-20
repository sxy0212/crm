<template>
  <section>
    <el-breadcrumb separator-class="el-icon-arrow-right" style="height:30px;font-size:14px;">
      <el-breadcrumb-item>首页</el-breadcrumb-item>
      <el-breadcrumb-item>投诉建议</el-breadcrumb-item>
    </el-breadcrumb>
    <div class="TableList" style="min-height: 550px; max-height: 650px; overflow: auto; padding:20px;background:#fff">
      <el-form ref="form1" :model="form1" :inline="true">
        <el-form-item label="选择服务器:">
         <el-select placeholder="请选择" v-model="form1.server_name" filterable>
            <el-option :label="item.ai" :value="item.ai" v-for="(item,index) in AI" :key="index"></el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="反馈类型:">
          <el-select placeholder="请选择" v-model="form1.type">
            <el-option label="全部" value="" ></el-option>
            <el-option label="建议" value="1" ></el-option>
            <el-option label="投诉" value="2" ></el-option>
          </el-select>
        </el-form-item>
        <el-form-item > 
          <el-button type="primary" @click="init(1)">查询</el-button>
          <el-button type="primary" @click="examineAll" :disabled="form1.server_name == ''">批量审核</el-button>
        </el-form-item>
      </el-form>
          <div class="TableList" style="margin-top: 10px;">
            <el-table ref="searchTable" :data="searchList" tooltip-effect="dark" style="width: 100%" border stripe v-loading="loading" @selection-change="handleSelectionChange">
             <el-table-column type="selection" width="50" align="center" :selectable='checkboxInit'></el-table-column>
              <el-table-column type="index" :index="indexMethod" label="序号" width="50" align="center"></el-table-column>
              <el-table-column prop="type" label="投诉/建议" width="90" align="center"> 
                <template slot-scope="scope">
                  <span v-show="scope.row.type==1">建议</span>
                  <span v-show="scope.row.type==2">投诉</span>
                </template>
              </el-table-column>
              <el-table-column prop="content" label="投诉/建议内容" align="center"></el-table-column>
               <el-table-column prop="server_name" label="服务器" width="160" align="center"></el-table-column>
              <el-table-column prop="admin_name" label="账号" width="160" align="center"></el-table-column>
               <el-table-column prop="create_time" label="创建日期" width="160" align="center"></el-table-column>
              <el-table-column label="反馈状态" width="100" align="center">
                <template slot-scope="scope">
                  <span v-show="scope.row.status==0">未处理</span>
                  <span v-show="scope.row.status==1">已处理</span>
                </template>
              </el-table-column>
               <el-table-column prop="update_time" label="处理日期" width="160" align="center"></el-table-column>
              <el-table-column label="操作" width="180" align="center">
                <template slot-scope="scope">
                  <el-button type="primary" plain  @click="examine(scope.row)" v-if="scope.row.status == '0'">回复</el-button>
                  <el-button type="primary" plain  @click="examine(scope.row)" v-else-if="scope.row.status == '1'" style="margin-left:0">查看</el-button>
                </template>
              </el-table-column>
            </el-table>
          </div>
          <div class="pagination" v-show="!!total">
            <div class="block"> 
              <el-pagination
                  @size-change="handleSizeChange"
                  @current-change="handleCurrentChange"
                  :current-page.sync="form1.page"    
                  :page-sizes="[10, 20, 30, 40]"
                  :page-size="form1.page_size"
                  layout="total, sizes, prev, pager, next, jumper"
                  :total="total">
              </el-pagination>
            </div>
          </div>
      </div>
     <!--审核/详情-->
    <div class="dial-header suggest">
      <el-dialog :title="title" :visible.sync="CustomerCust.distr" v-move>
        <div class="dialogueWord" style="padding:0 10px;">
          <div class="detail"> <span class="title">处理状态:</span>  <span v-if="detail.status == 0">未处理</span><span v-else>已处理</span></div>
          <div class="detail"> <span class="title">服务器:</span> {{detail.server_name}}</div>
          <div class="detail"> <span class="title">账号:</span> {{detail.admin_name}}</div>
          <div> <span class="title">时间:</span> {{detail.create_time}}</div>
          <div style="height:5px;width:100%;background:#d2d2d2;margin:20px 0;"></div>
          <div class="detailOne"> <span style="width:115px;">投诉内容:</span><span style="line-height:25px;border:1px solid #d2d2d2;padding:10px;width:590px;">{{detail.content}}</span> </div>
          <div class="detailOne" style="margin:20px 0"> 
            <span style="width:115px;">截图说明:</span>  
            <img :src="item" alt="" v-for="(item,index) in detail.pic_url"  @mouseenter="enter(item)" @mouseleave="leave()" class="img">
            <img :src="imgShow" style="width:500px;height:500px;position:fixed;z-index:10000;top:30%;left:55%;" v-show="img"> 
          </div>
           <div class="detailOne" v-show="detail.status == '1'"><span style="width:115px;">反馈结果:</span><span v-if="!detail.deal_info">暂无结果</span> <span v-else>{{detail.deal_info}}</span> </div>
           <div v-show="detail.status == '0'">
            <div style="margin-bottom:20px;">
              <span class="title">反馈内容:</span>
              <el-select placeholder="请选择" v-model="form.deal">
                <el-option label="暂无" value="暂无" ></el-option>
                <el-option label="您的投诉/建议我们已经收到，感谢您的及时反馈,我们会尽快处理！" value="您的投诉/建议我们已经收到，感谢您的及时反馈,我们会尽快处理！" ></el-option>
                <el-option label="自定义" value="自定义" ></el-option>
              </el-select>
            </div>
            <div v-show="form.deal == '自定义'" style="margin-bottom:20px;">
              <span class="title">自定义内容:</span>  <el-input v-model="form.deal_content" style="width:500px;"></el-input>
            </div>
            <el-button type="primary" @click="submitNow">确定</el-button>
            <el-button @click="CustomerCust.distr = false">取消</el-button>
           </div>
          
        </div> 
      </el-dialog>
    </div>
    <!--批量审核-->
    <div class="dial-header addMins">
      <el-dialog title="批量审核" :visible.sync="CustomerCust.Plays" v-move>
        <div class="dialogueWord" style="padding:0 10px;">
           <div >
            <div style="margin-bottom:20px;">
              <span class="title">反馈内容:</span>
              <el-select placeholder="请选择" v-model="formAll.deal">
                <el-option label="暂无" value="暂无" ></el-option>
                <el-option label="您的投诉/建议我们已经收到，感谢您的及时反馈,我们会尽快处理！" value="您的投诉/建议我们已经收到，感谢您的及时反馈,我们会尽快处理！" ></el-option>
                <el-option label="自定义" value="自定义" ></el-option>
              </el-select>
            </div>
            <div v-show="formAll.deal == '自定义'" style="margin-bottom:20px;">
              <span class="title">自定义内容:</span>  <el-input v-model="formAll.deal_content" style="width:500px;"></el-input>
            </div>
            <el-button type="primary" @click="submitNowAll">确定</el-button>
            <el-button @click="CustomerCust.Plays = false">取消</el-button>
           </div>
          
        </div> 
      </el-dialog>
    </div>
  </section>
</template>

<script>
  import {axiosRequest} from '@/assets/js/Yt.js'
  export default {
		data() {
			return {
        CustomerCust:{
          distr: false,
          Plays: false
        },
        AI:[],     //服务器
       
        currentPage:1,
        total:0,
        id:"",
        status:"", 
        searchList:[],         
        img:false,
        imgShow:"",
        detail:{},
        loading:true,
        form:{
          deal:"暂无",
          deal_content:"",
          id:""
        },
        title:"",
        form1:{
          page:1,
          page_size: 10,
          server_name:"",
          type:""
        },
        formAll:{
          deal:"暂无",
          deal_content:"",
          ids:"",
          order_id:"",
          deal_info:"",
        },
        multipleSelection:[]
        
			}
		},
    beforeMount() {
      this.init(0)
      this.initAI()
    },
		methods: {
      indexMethod(val){
        return (this.form1.page-1)*this.form1.page_size + val + 1
      },
      initAI(){
        const url = "/api_backend.php?r=call-statics/platform"
        const conf = {
          url,
          success:(data)=>{
            this.AI = data.info.ai
            console.log(data)
          }
        }
        axiosRequest(conf)
      },
      //初始化
      init(num){
        const url = "/api_backend.php?r=suggestion/index"
        const data = this.form1
        if(num == 0){
          data.page = this.form1.page
          data.page_size = this.form1.page_size
        }else{
          data.page = 1
          data.page_size = 10
        }
        const conf = {
          url,
          data:data,
          success:(data)=>{
            this.loading = false
            if( data.statusCode == 1 ){
                this.searchList = data.info.info
                this.total = Number(data.info.total_count)
            }else{
              this.searchList = []
              this.total = 0
            }
          }
        }
        axiosRequest(conf)
      },
          handleSizeChange(val) {
            this.form1.page_size = val
            this.init(0)
          }, 
          handleCurrentChange(val) {
            this.foem1.page = val
            this.init(0)
          },
          // 审核（内容）
          examine(row){
            this.CustomerCust.distr = true
            this.form.id = row.id
            const url = "/api_backend.php?r=suggestion/suggest-show"
            const conf  = {
              url,
              data:{
                id:row.id
              },
              success:(data)=>{
                this.detail  = data.info
                if(data.info.status == '1'){
                  this.title = '投诉/建议详情'
                }else{
                  this.title = "投诉/建议审核"
                }
                if(data.info.deal_info !='暂无'&&data.info.deal_info !='您的投诉/建议我们已经收到，感谢您的及时反馈,我们会尽快处理！'){
                  this.form.deal == '自定义'
                  this.form.deal_content = data.info.deal_info
                }else{
                  this.form.deal = data.info.deal_info
                }
                console.log(this.form.deal)
              }
            }
           axiosRequest(conf)
          },
          // 确定审核
          submitNow(){
            const url = "/api_backend.php?r=suggestion/verify"
            var deal_info = ""
            if(this.form.deal == '自定义'){
              deal_info = this.form.deal_content
            }else{
              deal_info = this.form.deal
            }
            const conf = {
              url,
              data:{
                deal_info:deal_info,
                id:this.form.id
              },
              success:(data)=>{
                this.$alert(data.message)
                this.CustomerCust.distr = false
                this.init()
              }
            }
            axiosRequest(conf)
          },
          handleSelectionChange(val) {
            this.multipleSelection = val
          },
          checkboxInit(row,index){
            if (this.form1.server_name) {//你需要判断的条件
              return 1;//可勾选
            }else{
              return 0;//不可勾选
            }
          },
          // 批量审核
          examineAll(){
            this.CustomerCust.Plays = true;
            this.formAll.deal = '暂无' 
          },
          submitNowAll(){
             const url = "/api_backend.php?r=suggestion/batch-withdraw"
            this.formAll.ids = this.multipleSelection.map(item=>{
                    return item.id
              }).join(",")
              this.formAll.order_id = this.multipleSelection.map(item=>{
                    return item.order_id
              }).join(",")
            var deal_info = ""
            if(this.formAll.deal == '自定义'){
              deal_info = this.formAll.deal_content
            }else{
              deal_info = this.formAll.deal
            }
            const conf = {
              url,
              data:{
                deal_info:deal_info,
                ids:this.formAll.ids,
                type:this.form1.type,
                server_name:this.form1.server_name,
                order_id:this.formAll.order_id
              },
              success:(data)=>{
                this.$alert(data.message)
                this.CustomerCust.Plays = false
                this.init()
              }
            }
            axiosRequest(conf)
          },
          // 移入移出效果
          enter(index){
            this.img = true
            this.imgShow = index
          },
          leave(){
            this.img = false
          },
	  }
	}
</script>
<style lang="scss" scoped>
.up {li{list-style:none;float:left;}}
.dialogueWord{font-size:16px}
.detail{height:30px;}
.detailOne{display:flex;}
.detailOne .img{width:100px;height:100px;border:1px solid #d2d2d2;padding:5px;margin-left:5px;}
.detailOne .img:nth-child(2){margin-left:0}
.title{display:inline-block; width:115px;}
.el-form-item__label{text-align:left}
  
</style>