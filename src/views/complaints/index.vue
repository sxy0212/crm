<template> 
 <section>
    <div class="complaints">
        <el-breadcrumb separator-class="el-icon-arrow-right" style="height:30px;font-size:14px;">
            <el-breadcrumb-item>首页</el-breadcrumb-item>
            <el-breadcrumb-item>投诉建议</el-breadcrumb-item>
        </el-breadcrumb>
         <div class="TableList" style="min-height: 550px; max-height: 650px; overflow: auto; padding:20px;background:#fff">
            <div class="button_list">
                <el-button type="info"    @click="init(1)">未处理</el-button>
                <el-button type="success" @click="init(2)">已采纳</el-button>
                <el-button type="danger" @click="init(3)">已拒绝</el-button>
            </div>
            <el-table :data="complaints" border ref="multipleTable">
                <el-table-column type="index" label="序号" width="60" align="center"></el-table-column>
                <el-table-column prop="suggestion" label="建议" align="left"></el-table-column> 
                <el-table-column prop="name" label="姓名" align="center"></el-table-column> 
                <el-table-column prop="mobile" label="联系方式" align="center"></el-table-column>
                <el-table-column prop="remark" label="备注内容" align="center"></el-table-column>
                <el-table-column  label="处理结果" align="center">
                    <template slot-scope="scope">
                        <span v-if="scope.row.deal_status==1" class="info"  >未处理</span>
                        <span v-else-if="scope.row.deal_status==2" class="success">已采纳</span>
                        <span v-else class="danger">已拒绝</span>
                    </template> 
                </el-table-column> 
                <el-table-column  label="操作" align="center" class="button"  v-if="status==null">
                    <template slot-scope="scope">
                        <el-button type="success" size="mini"  @click="submit(scope.row.id,2)" >采纳</el-button>
                        <el-button type="danger"  size="mini"  @click="submit(scope.row.id,3)" >拒绝</el-button>
                    </template>
                </el-table-column>

            </el-table>
            <el-pagination
                background
                style="padding-top:15px"
                layout="prev, pager, next"
                :total="total">
            </el-pagination>
         </div>
    </div>
   </section>  
</template>

<script>
  import {axiosRequest} from '@/assets/js/Yt.js'
export default {
    name: 'Complaints',
    data() {
        return  {
            complaints:[],
            typeForm:{
                type:0,
                page:1,
            }, 
            total:null,
            status:null,
        }
    },
    created() {
        this.init(4);
    },
    methods: {
        init(num) {
            const data  = this.typeForm;
            if(num==4) {
                data.page = this.typeForm.page;
            }else{
                this.typeForm.type = num;
                data.type =  this.typeForm.type;
                data.page = 1;
                this.status = 1;
            } 
            const url = "/api_backend.php?r=new-suggestion/show";
            const conf = {
                url,
                data:data,
                success:(data) => {
                    if(data.statusCode==1) {
                        this.complaints = data.info;
                        this.total = parseInt(data.total)
                    }   
                }
            } 
            axiosRequest(conf) 
        },
        submit(id,status){
            const url = "/api_backend.php?r=new-suggestion/update";
            console.log(id,status);
            const conf = {
                url,
                data:{'id':id,'deal_status':status},
                success:(data) => {
                   if(data.statusCode==1) {
                        this.$message({
                            message: '操作成功',
                            type: 'success'
                        });
                        this.status=1;
                        this.init(4)
                    }else{
                       this.$message.error('错了哦，这是一条错误消息');
                    }  
                }
            }
            axiosRequest(conf) 
        }
        
    }
}
</script>
<style scoped>
.button_list{ height: auto; overflow: hidden; margin-bottom: 15px}
span.info{color: #909399}
span.success{color: #67c23a}
span.danger{color: #f56c6c}
</style>


