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
          prop="admin_name"
          label="账号"
          >
        </el-table-column>
        <el-table-column
          prop="create_time"
          label="时间"
          >
        </el-table-column>
    </el-table>
</template>
<script>

export default {
  name:'tableClickRate',
  props:[
      'templateList',
      'page',
      'page_size'
  ],
  methods:{
    indexMethod(val){
      return (this.page-1)*this.page_size + val + 1
    },
  }
}
</script>

