<template>
<div>
    <div>
        <el-breadcrumb separator-class="el-icon-arrow-right" style="height:30px;font-size:14px;">
        <el-breadcrumb-item>首页</el-breadcrumb-item>
        <el-breadcrumb-item>广告详情</el-breadcrumb-item>
        </el-breadcrumb>
    </div>
  <div class="cover templateMan">
    <div style="color:red;"> 
        点击量：{{total}}
    </div>
    <div class="tableCover">
        <div-table
            :templateList='tableData'
            :page="page"
            :page_size ="page_size"
        >
        </div-table>
        <page-change 
            :total="total"
            :page="page"
            :page_size ="page_size"
            v-on:pageSizeChange='pageSizeChangeFn($event)'
            v-on:currentPageChange='currentPageChangeFn($event)'
            >
        </page-change>
    </div>
  </div>
</div>
</template>
<script>


import tableClickRate from '@/functions/tableCollection/tableClickRate.vue'
import pageChange from '@/components/pageChange.vue'
import  { axiosRequest } from '@/assets/js/Yt.js'
import  { axiosParams } from '@/assets/js/Yt.js'
import { Message } from 'element-ui'


export default {
    components:{
        'page-change':pageChange,
        'div-table':tableClickRate
    },
    data() {
        return {
            page:1,
            page_size:10,
            total:0,
            tableData: [],
            id:''
        }
    },
    created(){
        this.id = this.$route.query.id
        this.init()
    },
    methods: {
        init(){
            const conf = {
                url : '/api/api_backend.php?r=poster/post-stat',
                data:{
                    page:this.page,
                    page_size:this.page_size,
                    id:this.id
                },
                success:(data)=>{
                    if( data.statusCode == 1 ){
                        this.tableDate = data.info.info 
                        this.total = ParseInt(data.total)
                    }else {
                        Message({
                            message: data.message,
                            type: 'erro',
                            duration: 3 * 1000
                        })
                    }
                }
            }
            axiosRequest(conf)
        },
        pageSizeChangeFn(val){
            this.page_size = val
            this.init()
        },
        currentPageChangeFn(val){
            this.page = val
            this.init()
        },
    }
}
</script>
<style scoped>

</style>



