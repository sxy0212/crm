<template>
<section>
   <el-form :model="ruleForm2"  label-position="left" label-width="0px" class="demo-ruleForm login-container">
    <h3 class="title">代理商后台</h3>
    <el-form-item prop="account">
      <el-input type="text" v-model="ruleForm2.username" auto-complete="off" placeholder="账号"></el-input>
    </el-form-item>
    <el-form-item prop="checkPass">
      <el-input type="password" v-model="ruleForm2.password" auto-complete="off" placeholder="密码"></el-input>
    </el-form-item>
    <el-form-item style="width:100%;">
      <el-button type="primary" style="width:100%;" @click="handleReset2" > <i class="el-icon-loading" v-show="logining"></i> 立即登录</el-button>
    </el-form-item>
  </el-form>
</section>
 
</template>

<script>
  import axios from "axios"
  import {axiosRequest} from '@/assets/js/Yt.js'
  import {getCookie} from '@/assets/js/Yt.js'
  import {setCookie} from '@/assets/js/Yt.js'
  export default {
    data() {
      return {
        logining: false,
        ruleForm2: {
          username: '',
          password: ''
        },
        checked: true,
        dialogVisible:true
      };
    },
    methods: {
      handleReset2() {
        this.logining = true
        const username = this.ruleForm2.username
        const password = this.ruleForm2.password
        const url = "/api_backend.php?r=index/checklogin"
        const conf = {
          url,
          data:{
            username,
            password
          },
          success:(data)=>{
            if(data.statusCode == 1){
              setCookie('user',2);
              this.$router.push({ path: '/index' });
              this.loading = false
            }else{
              this.$alert(data.message)
              this.logining = false
            }
          }
        }
        axiosRequest(conf)
      },
    },
  }

</script>

<style lang="scss" scoped>
  .login-container {
    /*box-shadow: 0 0px 8px 0 rgba(0, 0, 0, 0.06), 0 1px 0px 0 rgba(0, 0, 0, 0.02);*/
    -webkit-border-radius: 5px;
    border-radius: 5px;
    -moz-border-radius: 5px;
    background-clip: padding-box;
    margin: 180px auto;
    width: 350px;
    padding: 35px 35px 15px 35px;
    background: #fff;
    border: 1px solid #eaeaea;
    box-shadow: 0 0 25px #cac6c6;
    .title {
      margin: 0px auto 40px auto;
      text-align: center;
      color: #505458;
    }
    .remember {
      margin: 0px 0px 35px 0px;
    }
  }
</style>