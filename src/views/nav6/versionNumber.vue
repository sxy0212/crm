<template>
<div>
<div>
    <el-breadcrumb separator-class="el-icon-arrow-right" style="height:30px;font-size:14px;">
      <el-breadcrumb-item>首页</el-breadcrumb-item>
      <el-breadcrumb-item>系统版本号</el-breadcrumb-item>
    </el-breadcrumb>
</div>
  <div class="cover templateMan">
    <div class="tableCover">
      <el-form :model="android_version" status-icon  label-width="100px" class="demo-ruleForm" style="float:left">
       <el-form-item label="安卓">
        </el-form-item>
        <el-form-item label="版本号">
          <el-input  v-model="android_version.system_version" autocomplete="off" style="width:200px"></el-input>
        </el-form-item>
        <el-form-item label="更新说明">
          <el-input  v-model="android_version.update_instructions" autocomplete="off" style="width:200px" type="textarea" :rows="5"></el-input>
        </el-form-item>
        <el-form-item label="下载链接:">
          <el-input  v-model="android_version.update_link" autocomplete="off" style="width:200px" ></el-input>
        </el-form-item>
        <el-form-item label="是否强制更新:">
            <el-radio-group v-model="android_version.is_mandatory_update">
              <el-radio :label="1">是</el-radio>
              <el-radio :label="0">否</el-radio>
            </el-radio-group>
        </el-form-item>
      </el-form>
        <el-form :model="ios_version" status-icon  label-width="100px" class="demo-ruleForm" style="float:left;margin-left:20px;">
        <el-form-item label="ios">
        </el-form-item>
        <el-form-item label="版本号">
          <el-input  v-model="ios_version.system_version" autocomplete="off" style="width:200px"></el-input>
        </el-form-item>
        <el-form-item label="更新说明">
          <el-input  v-model="ios_version.update_instructions" autocomplete="off" style="width:200px" type="textarea" :rows="5"></el-input>
        </el-form-item>
        <el-form-item label="下载链接:">
          <el-input  v-model="ios_version.update_link" autocomplete="off" style="width:200px" ></el-input>
        </el-form-item>
         <el-form-item label="是否强制更新:">
            <el-radio-group v-model="ios_version.is_mandatory_update">
              <el-radio :label="1">是</el-radio>
              <el-radio :label="0">否</el-radio>
            </el-radio-group>
        </el-form-item>
        <el-form-item label="密码">
          <el-input type="password" v-model="validation_surface" autocomplete="off" style="width:200px"  @keyup.enter.native='sure'></el-input>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="sure">提交</el-button>
        </el-form-item>
      </el-form>
    </div>
   
  </div>
  </div>
</template> 
<script>


import  { axiosRequest} from '@/assets/js/Yt.js'
import { Message } from 'element-ui'
import router from '@/router.js'
 export default {
    data() {
      return {
        validation_surface: '',
        ios_version: {
          system_version: '',
          update_instructions:"",
          is_mandatory_update:0,
          update_link:""
        },
        android_version: {
          system_version: '',
          update_instructions:"",
          is_mandatory_update:0,
          update_link:""
        },
      };
    },
    mounted() {
      this.init()
    },
    methods: {
       init(){
            const conf = {
                url : '/api_backend.php?r=system-version/index',
                success:(data)=>{
                   this.ios_version = JSON.parse(data.info.ios_version)
                   this.ios_version.is_mandatory_update  = Number(JSON.parse(data.info.ios_version).is_mandatory_update)
                   this.android_version= JSON.parse(data.info.android_version)
                   this.android_version.is_mandatory_update  = Number(JSON.parse(data.info.android_version).is_mandatory_update)
                }
            }
            axiosRequest(conf)
        },
        // 这是安卓设备的更新说明
        sure(){
            const conf = {
                url : '/api_backend.php?r=system-version/index',
                data:{
                  ios_version:JSON.stringify(this.ios_version),
                  android_version:JSON.stringify(this.android_version),
                  validation_surface:this.validation_surface
                },
                success:(data)=>{
                  this.$alert(data.message)
                  this.init()
                  this.validation_surface = ""
                }
            }
            axiosRequest(conf)
        },
    }
  }
</script>
<style scoped>
.addAdvertisement{margin-bottom:15px;}
</style>



