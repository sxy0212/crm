
<template>
  <div id="app">
     <el-container v-if="$route.meta.keepAlive">
  
      <el-header>
        <keep-alive>
        <!-- 导航栏 -->
          <top></top>
         
        </keep-alive> 
         <NaveTab style="margin-left:250px;"></NaveTab> 
      </el-header>
    <el-container v-if="$route.meta.keepAlive">
      <el-aside style="background-color: #EBEBEB;width:250px;">
        <!-- 侧边栏 -->
        <keep-alive>
          <left></left>
        </keep-alive>
      </el-aside>
      <el-main style="min-height:550px;max-height:750px;overflow:auto;margin-top:60px;">
        <!-- Body -->
         <router-view v-if="isRouterAlive"></router-view>
      </el-main>
    </el-container>
    </el-container>
    <!-- 登录页 -->
    <router-view v-if="!$route.meta.keepAlive"></router-view>
  </div>
</template>

<script>
import left from './views/Left'
import top from './views/Top'
import NaveTab from './views/NaveTab/index'
export default {
  name: 'App',
  provide(){
    return {
      reload:this.reload
    }
  },
  data(){
    return {
      isRouterAlive:true
    }
  },
  components:{
    left,
    NaveTab,
    top
  },
  beforeMount() {
    this.upload()
  },
  methods:{
    upload(){     //按F5刷新时进去index页面同时要保证套转到dataShow页面时不是index页面
      if(this.$route.path != '/dataShow' || this.$route.path == '' ){
        this.$router.push("/index")
      }
    },  
    reload(){            //单页面刷新
    //  location.replace("/#/form");
    // location.reload()
    console.log(this.$route.path)
    this.$router.push(this.$route.path)
      console.log('刷新')
      this.isRouterAlive = false
      this.$nextTick(function(){
        this.isRouterAlive = true
      })
    } ,

  }
} 
</script>

<style>
html,body,p,h1,h2,h3,h4,h5{ padding:0; margin:0; }
#app { font-family:'微软雅黑', 'Avenir', Helvetica, Arial, sans-serif; -webkit-font-smoothing: antialiased; -moz-osx-font-smoothing: grayscale; background:#f2f2f2; }
#app .el-header{ padding:0 0; }
#app a{ text-decoration: none; }
.el-main{ background:#f2f2f2; }
#tabs  .el-tabs__item{ font-size:2.2rem !important; }
#tabs .el-tabs--border-card>.el-tabs__header .el-tabs__item.is-active{ background:none!important; }
#tabs .el-table .cell{ font-weight: bolder;line-height:45px !important;height:45px; }
#tabs .el-tabs__nav-scroll{ font-size:2.2rem !important;font-weight: bolder; }
.suggest .el-dialog{ width:820px;min-width:820px; }
#dataShow .el-tabs__content{ padding:0px; }
#dataShow .el-tabs--border-card>.el-tabs__header{ background:#000; }
#dataShow .el-tabs__header .el-tabs__item{ color:#fff; }
#dataShow .el-tabs--border-card>.el-tabs__header .el-tabs__item.is-active{ color:#409EFF; }
#dataShow .el-table__empty-block{ background:#000; }
#dataShow .el-table__empty-text{ color:#fff; }
#dataShow .el-table__body-wrapper{ background:#000;color:#fff; }
html,body,#app,.el-container{ height:100%; }
.el-pagination{ text-align:center; }
.el-table .cell, .el-table th>.cell { text-align: center; }
</style>