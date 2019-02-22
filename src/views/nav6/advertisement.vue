<template>
<div>
<div>
    <el-breadcrumb separator-class="el-icon-arrow-right" style="height:30px;font-size:14px;">
      <el-breadcrumb-item>首页</el-breadcrumb-item>
      <el-breadcrumb-item>广告添加</el-breadcrumb-item>
    </el-breadcrumb>
</div>
  <div class="cover templateMan">
    
    <div> 
        <el-button type="success" @click='addFn(true)' class="addAdvertisement">添加广告</el-button>
    </div>
    <div class="tableCover">
        <div-table
            :templateUse='templateUse'
            :templateList='tableData'
            :page="page"
            :page_size ="page_size"
            v-on:editFn='editFn($event)'
            v-on:deleteFn='deleteFn($event)'
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
    <el-dialog :title="bannerTitle" :visible.sync="addNow" v-move :close-on-click-modal=false :close-on-press-escape=false>
        <edit-dialog
            v-on:addNowChange="addFn($event)"
            v-on:saveFn="init($event)"
            v-on:protectFn="protectFn($event)"
            :position_id="position_id"
            :formTitle="formTitle"
            :bannerTitle="bannerTitle"
            :fieldsList="fieldsList"
        ></edit-dialog>
    </el-dialog>
  </div>
  </div>
</template>
<script>

import addAdvertisement from '@/functions/editDialog/addAdvertisement.vue'
import tableAdvertisement from '@/functions/tableCollection/tableAdvertisement.vue'
import pageChange from '@/components/pageChange.vue'
import  { axiosRequest } from '@/assets/js/Yt.js'
import  { axiosParams } from '@/assets/js/Yt.js'
import { Message } from 'element-ui'


export default {
    components:{
        'edit-dialog':addAdvertisement,
        'page-change':pageChange,
        'div-table':tableAdvertisement
    },
    data() {
        return {
            id:'',  //具体某一条
            position_id:'',//编辑还是添加
            templateUse:true,
            page:1,
            page_size:10,
            total:0,
            bannerTitle:"添加广告",
            addNow:false,
            tableData: [],
            formTitle:{//添加或是修改模块的数据
                custom_name:'',
                position_id:'',
                expire_time:'',
                href:'',
                upload_name:'',
                save_path:'',
                imageUrl:''
            },
            fieldsList:[],//多选选项
        }
    },
    created(){
        this.init()
    },
    methods: {
        init(){
            const conf = {
                url : '/api/api_backend.php?r=poster/poster',
                data:{
                    page:this.page,
                    page_size:this.page_size
                },
                success:(data)=>{
                    if( data.statusCode == 1 ){
                        this.tableData = data.info.info.map(item=>{
                            item.save_path = 'http://agent.com' + item.save_path  //图片加上域名
                            return item
                        })
                        this.total = Number( data.info.total_count )
                    }
                }
            }
            axiosRequest(conf)
        },
        getFieldsList(){
            const conf = {
                url : '/api/api_backend.php?r=poster/position',
                success:(data)=>{
                    if( data.statusCode == 1 ){
                        this.fieldsList = data.info
                    }else {
                        this.fieldsList = []
                    }
                }
            }
            axiosRequest(conf)
        },
        addFn(val){//添加弹框的打开与关闭
            this.bannerTitle = "添加广告"
            this.getFieldsList()
            this.addNow = val
            this.formTitle = {
                custom_name:'',
                position_id:'',
                expire_time:'',
                href:'',
                upload_name:'',
                save_path:'',
                imageUrl:''
            }
        },
        editFn(row){//编辑弹框的打开与关闭
            this.getFieldsList()
            const conf = {
                url : '/api/api_backend.php?r=poster/post-show',
                data:{
                    id:row.id
                },
                success:(data)=>{
                    if( data.statusCode == 1 ){
                        this.bannerTitle = "编辑广告"
                        this.addNow = true
                        this.id = row.id
                        let { custom_name,position_id,expire_time, href,upload_name,save_path} = data.info
                        this.formTitle = { custom_name,position_id,expire_time, href,upload_name,save_path}
                        this.formTitle.imageUrl = "http://agent.com" + this.formTitle.save_path
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
        deleteFn(row){
             this.$confirm('确定删除这一条？', '提示', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(() => {
                let conf = {
                    url : '/api/api_backend.php?r=poster/post-del',
                    data : {
                        id:row.id
                    },
                    success:(data)=>{
                        if( data.statusCode == 1 ){
                            this.init()
                            Message({
                                message: data.message,
                                type: 'success',
                                duration: 3 * 1000
                            })
                        }else if(data.statusCode == 0){
                            Message({
                                message: data.message,
                                type: 'erro',
                                duration: 3 * 1000
                            })
                        }
                    }
                }
                axiosRequest(conf)
            }).catch( () =>{
                Message({
                    message:'取消删除',
                    type: 'erro',
                    duration: 3 * 1000
                })
            })
        },
        downloadFn(row){
            window.open('/api/api_backend.php?r=system-setting/template-download&id=' + row.id)
        },
        protectFn(){    //添加图片  
            this.formTitle.id = this.id  
            const data = this.formTitle
            let url 
            if( this.bannerTitle == '添加广告' ){
                url = '/api/api_backend.php?r=poster/post-add'
            }else if( this.bannerTitle == "编辑广告" ){
                url = '/api/api_backend.php?r=poster/post-edit'
            }
            const conf = {
                url,
                data,
                success:(data)=>{
                    if( data.statusCode == 1 ){
                        this.addNow = false
                        this.init()
                        this.bannerTitle = ''
                        this.id = ''
                        this.formTitle = {
                            custom_name:'',
                            position_id:'',
                            expire_time:'',
                            href:'',
                            upload_name:'',
                            save_path:'',
                            imageUrl:''
                        }
                        Message({
                            message: data.message,
                            type: 'success',
                            duration: 3 * 1000
                        })
                    }else if(data.statusCode == 0){
                        Message({
                            message: data.message,
                            type: 'erro',
                            duration: 3 * 1000
                        })
                    }
                }
            }
            axiosRequest(conf)
        }
    }
}
</script>
<style scoped>
.addAdvertisement{margin-bottom:15px;}

</style>



