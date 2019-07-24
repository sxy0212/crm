<template>
	<section style="font-size:20px;background:#000;color:#fff" id="dataShow">
		<div class="title">
      言通智能机器人-信息公告板
    </div>
    <div id="tabs">
      <el-tabs v-model="activeName"  @tab-click=" handleBtnClick" type="border-card">
          <el-tab-pane label="余额告警客户" name="1">
            <el-table :data="tableData1"  stripe style="width: 100%;" border stripe height="800" v-loading="loading" :header-cell-style="RowStyle" header-row-class-name="table" :cell-style="RowStyle" >
                <el-table-column type="index" label="序号" :index="index" width="100" align="center"></el-table-column>                   
                <el-table-column prop="account" label="账户名" align="center"></el-table-column>
                <el-table-column prop="money" label="余额" align="center"></el-table-column>
            </el-table>
          </el-tab-pane>
          <el-tab-pane label="机器人到期客户" name="2">
            <el-table :data="tableData2" stripe style="width: 100%" border stripe height="800" :header-cell-style="RowStyle" header-row-class-name="table" :cell-style="RowStyle">
                <el-table-column type="index" :index="index2" label="序号" width="100" align="center"></el-table-column>                   
                <el-table-column prop="company_name" label="机构名称" align="center"></el-table-column>
                <el-table-column prop="admin_name" label="管理员名称" align="center"></el-table-column>
                <el-table-column prop="number" label="机器人编号" align="center"></el-table-column>
                <el-table-column prop="auth_time" label="机器人授权时间" align="center"></el-table-column>
            </el-table>
          </el-tab-pane>
          <el-tab-pane label="已过期账户" name="3">
            <el-table :data="tableData3" stripe style="width: 100%" border stripe height="800" :header-cell-style="RowStyle" header-row-class-name="table" :cell-style="RowStyle"> 
                <el-table-column type="index"  label="序号" width="100" align="center"></el-table-column>  
                <el-table-column prop="servers" label="平台" align="center"></el-table-column>
                <el-table-column prop="company_name" label="机构名称" align="center"></el-table-column>
                <el-table-column prop="admin_name" label="管理员名称" align="center"></el-table-column>
            </el-table>
          </el-tab-pane>
          <el-tab-pane label="流量收入" name="4">
            <el-table :data="form4" stripe style="width: 100%" border stripe height="800" :header-cell-style="RowStyle" header-row-class-name="table" :cell-style="RowStyle"> 
                <el-table-column prop="total_account"  label="总账户数目"  align="center"></el-table-column>  
                <el-table-column prop="no_expire" label="未过期账户" align="center"></el-table-column>
                <el-table-column prop="expire" label="过期账户" align="center"></el-table-column>
                <el-table-column prop="running_robot" label="运行机器人数量" align="center"></el-table-column>
                <el-table-column prop="per_call_count" label="平均单日单台呼出量" align="center"></el-table-column>
                <el-table-column prop="per_called_count" label="平均接通率" align="center"></el-table-column>
                <el-table-column prop="per_sec_count" label="每日通话时长" align="center"></el-table-column>
            </el-table>
          </el-tab-pane>
          <el-tab-pane label="线路资费" name="5">
              <div style="height:800px;color: #fff;background: #000;font-size: 30px;">
                <div style="margin: 0 auto;width: 900px;">
                  <div style="margin-bottom:20px;padding-top:20px;">
                    <p>催收本人前期:1毛2 </p>
                    <p>催收本人中后期:1毛5（线路紧缺）</p>
                  </div>
                  
                  <p>游戏推广(不含博彩,赌博类):1毛</p>
                  <div style="margin:20px 0;">
                    <p>股票:1毛4</p>
                    <p>流量股票:1毛5</p>
                  </div>
                  
                  <div style="margin:20px 0;">
                    <p>贷款:1毛1</p>
                    <p>流量贷款:1毛2</p>
                  </div>
                 
                  <div style="margin:20px 0;">
                    <p>保健品:1毛</p>
                    <p>收藏品:1毛2（线路紧缺）</p>
                  </div>
                 
                  <div>
                     <p>非金融正规：</p>
                     <p>20并发内正规量乱显:最低8分</p>
                     <p>50并发内正规量小号:最低8分  月租10元/号/线(小号更换，当月补交一个月月租)</p>
                     <p>超20并发房产,考证培训:9分做</p>
                  </div>
                   <p style="background: yellow;color: #000;margin-top:30px;">涉及黄赌毒,代开发票,代办假证,棋牌麻将类游戏等,线路自备.</p>
                </div>
                
              </div>
          </el-tab-pane>
      </el-tabs>
    </div>
     
  
	</section>
</template>

<script>
  import {axiosRequest} from '@/assets/js/Yt.js'
	export default {
		data() {
			return {
        loading:true,
				activeName:"1",
        num:0,
        time:"",
        page1:0,
        page_size1:10,
        page_total1 : "",
        page2:0,
        page_size2:10,
        page_total2 : "",
        page3:0,
        page_size3:10,
        page_total3 : "",
        tableData1:[],
        tableData2:[],
        tableData3:[],
        form4:[],


			}
		},
    mounted() {
      this.handleBtnClick()
      // this.init1()
      this.init2()
      this.init3()
      this.init4()
      this.initData()
		},
		methods: {
       // 修改table tr行的背景色
    RowStyle({row, rowIndex}) {
      return 'background: #000;color:#fff'
    },
      index(val){
        return (this.page1-1)*this.page_size1+val+1
      },
      index2(val){
        return (this.page2-1)*this.page_size2+val+1
      },
      initData(){
        this.time = setInterval(this.handleBtnClick,25000)
      },
      // 余额告警
      init1(){       
        this.loading = true;
        this.tableData1 = []
        const url = "/api_backend.php?r=call-statics/balance"
        const conf = {
          url,
          data:{
            page:this.page1,
            page_size:this.page_size1
          },
          success:(data)=>{
            this.loading = false
            for(var item in data.info.info){
              this.tableData1.push(data.info.info[item])
            }
            this.page_total1 = parseInt(data.info.total_page)
          }
        }
        axiosRequest(conf)
      },
      // 即将过期
      init2(){
        const url = "/api_backend.php?r=call-statics/expire-robot"
        const conf = {
          url,
          data:{
            page:this.page2,
            page_size:this.page_size2
          },
          success:(data)=>{
            this.tableData2 = data.info.info
            this.page_total2 = parseInt(data.info.total_page)
          }
        }
         axiosRequest(conf)
      },
      // 已过期账户
      init3(){ 
        const url = "/api_backend.php?r=call-statics/account-expire"
        const conf = {
            url,
            data:{
              page:this.page3,
              page_size:this.page_size3
            },
            success:(data)=>{
              this.tableData3 = data.info.info
              if(data.info.total_page){
                this.page_total3 = parseInt(data.info.total_page)
              }else{
                 this.page_total3 = 0
              }
            }
          }
          axiosRequest(conf)
      },
      // 流量收入
      init4(){ 
        this.form4 = []
        const url = "/api_backend.php?r=call-statics/flow-show"
        const conf = {
            url,
            success:(data)=>{
              this.form4.push(data.info)
            }
          }
          axiosRequest(conf)
      },
			 handleBtnClick(){
         this.num ++;
         this.activeName = this.num.toString()
        if(this.activeName == 1){
           this.page1 ++
           if(this.page1 > parseInt(this.page_total1)){
             this.page1 = 1
           }
          this.init1()
        }else if(this.activeName == 2){  
          this.page2 ++
          if(this.page2 > parseInt(this.page_total2)){
             this.page2 = 1
           }
          this.init2()
         }else if (this.activeName == 3){
           this.page3 ++
          if(this.page3 > parseInt(this.page_total3)){
             this.page3 = 1
           }
            console.log(this.page3)
          this.init3()
        }else if (this.activeName == 4){
          this.init4()
        }else if (this.activeName >= 5){
          this.num = 0
        }
        // else if(this.activeName > 3){
        //   // console.log(4444)
        //  this.num = 0
        // }
        console.log(this.num)
        //  this.init1()
        
        
      },
		},
	
	}

</script>

<style lang="scss" scoped>
  #app{
    background-color:#f2f2f2;
  }
 .title{
   font-size:2.4rem;
   font-weight: bolder;
   padding-bottom:10px;
   padding-top:10px;
 }
 .el-table{
    font-size:30px !important;
    height: 800px;
    display: block;
    background: #000;
 }
 .iconTable .cell{
  width:200px;text-align:center;border: 1px solid #ebeef5;
  background:#000;
  color:#fff
 }
</style>