<template>
    <div class="addAdvertisement">
        <div>
           <el-form :model="formTitle">
                <el-form-item label="客户名称" label-width="80px">
                    <el-input v-model="formTitle.custom_name" autocomplete="off"></el-input>
                </el-form-item>
                <el-form-item label="选择位置" label-width="80px">
                    <el-select 
                        v-model='formTitle.position_id' 
                        placeholder="请选择广告位置"
                    >
                        <el-option
                            v-for="item in fieldsList"
                            :key="item.id"
                            :label="item.name"
                            :value="item.id">
                        </el-option>
                    </el-select>
                </el-form-item>
                <el-form-item label="到期时间" label-width="80px">
                    <el-date-picker
                        v-model="formTitle.expire_time"
                        type="date"
                        value-format='yyyy-MM-dd'
                        placeholder="选择日期">
                    </el-date-picker>
                </el-form-item>
                <el-form-item label="上传图片" label-width="80px" class="uploadContent">
                    <el-upload
                        class="avatar-uploader"
                        action="/api_backend.php?r=poster/upload"
                        :show-file-list="false"
                        accept=".gif,.jpg,.jpeg,.gif,.png,.bmp"
                        :on-success="handleAvatarSuccess"
                        >
                        <img v-if="imageUrl" :src="imageUrl" class="avatar">
                        <i v-else class="el-icon-plus avatar-uploader-icon"></i>
                        
                    </el-upload>
                    <span class="redUpload" v-if="formTitle.position_id == '1'">为了更好的展示效果，建议上传图片尺寸：90*50px</span>
                    <span class="redUpload" v-if="formTitle.position_id == '2'">为了更好的展示效果，建议上传图片尺寸：1640*150px</span>
                    <span class="redUpload" v-if="formTitle.position_id == '3'">为了更好的展示效果，建议上传图片尺寸：430*280px</span>
                </el-form-item>
                <el-form-item label="链接地址" label-width="80px">
                    <el-input v-model="formTitle.href" autocomplete="off"></el-input>
                </el-form-item>
            </el-form> 
        </div>
        <div slot="footer" class="dialog-footer">
            <el-button type="primary" @click="protectFn">保存</el-button>
            <el-button  @click="cancelFn">取消</el-button>
        </div>
    </div>
</template>
<script>
import { axiosRequest } from '@/assets/js/Yt.js'
import { Message } from 'element-ui'
export default {
    name:"addArea",
    data(){
        return {
            imageUrl:''
        }
    },
    created(){
        this.imageUrl = this.formTitle.imageUrl
    },
    props:[
        'position_id',
        'formTitle',//表单
        'fieldsList',//多选选项
        'bannerTitle'
    ],
    watch:{
        imageUrl(){
            return this.imageUrl
        }
    },
    methods:{
        cancelFn(){//更改菜单标题
            this.$emit("addNowChange",false)
            this.$emit("clearFormTitle")
        },
        protectFn(){
            this.$emit('protectFn')
        },
        handleAvatarSuccess(res, file) {
            if( res.statusCode == 1 ){
                this.formTitle.upload_name = res.info[0].upload_name
                this.formTitle.save_path = res.info[0].save_path
                this.imageUrl = URL.createObjectURL(file.raw)
            }else {
                Message({
                    message: res.message,
                    type: 'erro',
                    duration: 3 * 1000
                })
            }
        },
    }
}
</script>

<style>
.addAdvertisement .avatar-uploader .el-upload {
    border: 1px dashed #d9d9d9;
    border-radius: 6px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
}
.addAdvertisement .avatar-uploader .el-upload:hover {
    border-color: #409EFF;
}
.addAdvertisement  .avatar-uploader-icon {
    font-size: 28px;
    color: #8c939d;
    width: 178px;
    height: 178px;
    line-height: 178px;
    text-align: center;
}
.addAdvertisement  .avatar {
    width: 178px;
    height: 178px;
    display: block;
}
.addAdvertisement  .redUpload{color:red; font-weight:450;}
.addAdvertisement .uploadContent .el-form-item__content{line-height:23px;}
</style>





