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
      版本号:
      <el-input style="width:300px" v-model="version"></el-input>
       <el-button type="primary" @click="sure">确定</el-button>
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
           version:""
        }
    },
    created(){
        this.init()
    },
    methods: {
        init(){
            const conf = {
                url : '/api/batch.php?r=system-version/index',
                success:(data)=>{
                   this.version  = data.info
                }
            }
            axiosRequest(conf)
        },
        sure(){
             const conf = {
                url : '/api/batch.php?r=system-version/index',
                data:{
                  system_version:this.version
                },
                success:(data)=>{
                  this.$alert(data.message)
                  this.init()
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



