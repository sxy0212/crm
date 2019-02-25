<template>
    <el-table
        border
        :data="templateList"
        style="width: 98%">
        <el-table-column
          type="index"
          :index="indexMethod"
          label="序号"
          width="55"
          >
        </el-table-column>
        <el-table-column
          prop="custom_name"
          label="客户名称"
          >
        </el-table-column>
        <el-table-column
          prop="position_name"
          label="位置"
          >
        </el-table-column>
        <el-table-column
          prop="create_time"
          label="上传时间"
          >
        </el-table-column>
        <el-table-column
          prop="expire_time"
          label="到期时间"
          >
        </el-table-column>
        <el-table-column
          prop="status"
          label="状态"
          >
            <template slot-scope="scope">
              <span v-if="scope.row.status==0">未使用</span>
              <span v-if="scope.row.status==1">展示中</span>
              <span v-if="scope.row.status==2">已过期</span>
            </template>
        </el-table-column>
        <el-table-column
          prop="save_path"
          label="预览"
          >
            <template slot-scope="scope">
              <el-popover
                placement="bottom"
                title=""
                trigger="hover"
                >
                <img :src="scope.row.save_path" style="max-width: 300%;"/>
                <img slot="reference" :src="scope.row.save_path" :alt="scope.row.upload_name" style="max-height: 50px;max-width: 130px">
              </el-popover>
            </template>
        </el-table-column>
        <el-table-column
          label="操作"
          width="250"
          >
          <template slot-scope="scope">
            <el-button size="mini" type="success" round @click="editFn(scope.row)" v-show="templateUse">编辑</el-button>
            <el-button size="mini" type="danger" round @click="deleteFn(scope.row)" v-show="templateUse">删除</el-button>
          </template>
        </el-table-column>
      </el-table>
</template>
<script>

export default {
  name:'tableAdvertisement',
  props:[
      'templateList',
      'templateUse',//决定给哪个模块使用
      'page',
      'page_size'
  ],
  methods:{
    indexMethod(val){
      return (this.page-1)*this.page_size + val + 1
    },
    editFn(val){
      this.$emit('editFn',val)
    },
    downloadFn(val){
      this.$emit('downloadFn',val)
    },
    deleteFn(val){
      this.$emit('deleteFn',val)
    },
    overImgFn(){

    },
    outImgFn(){

    }
  }
}
</script>

