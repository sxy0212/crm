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
      <el-form :model="ruleForm2" status-icon  label-width="100px" class="demo-ruleForm">
        <el-form-item label="版本号">
          <el-input  v-model="ruleForm2.version" autocomplete="off" style="width:200px"></el-input>
        </el-form-item>
        <el-form-item label="更新说明">
          <el-input  v-model="ruleForm2.update_instructions" autocomplete="off" style="width:200px"></el-input>
        </el-form-item>
        <el-form-item label="密码">
          <el-input type="password" v-model="ruleForm2.validation_surface" autocomplete="off" style="width:200px"  @keyup.enter.native='sure'></el-input>
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
        ruleForm2: {
          version: '',
          update_instructions:"",
          validation_surface: '',
        },
      };
    },
    mounted() {
      this.init()
    },
    methods: {
       init(){
            const conf = {
                url : '/api/api_backend.php?r=system-version/index',
                success:(data)=>{
                   this.ruleForm2.version  = data.info.system_version
                   this.ruleForm2.update_instructions = data.info.update_instructions
                }
            }
            axiosRequest(conf)
        },
        sure(){
          if(!this.ruleForm2.version||!this.ruleForm2.validation_surface){
            this.$alert('版本号或者密码不能为空!')
          }else{
            const conf = {
                url : '/api/api_backend.php?r=system-version/index',
                data:{
                  system_version:this.ruleForm2.version,
                  update_instructions:this.ruleForm2.update_instructions,
                  validation_surface:this.ruleForm2.validation_surface
                },
                success:(data)=>{
                  this.$alert(data.message)
                  this.init()
                  this.ruleForm2.validation_surface = ''
                }
            }
            axiosRequest(conf)
          }
        },
    }
  }
</script>
<style scoped>
.addAdvertisement{margin-bottom:15px;}
</style>



