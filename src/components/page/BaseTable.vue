<template>
<div class="basetable">
  <el-upload
  class="upload-demo"
  action="123"
  :before-upload="beforeUpload">
  <el-button size="small" type="primary">点击上传</el-button>
  <div slot="tip" class="el-upload__tip">只能上传内容为json格式的文本</div>
  </el-upload>
  <el-button size="small" type="primary" @click="expto">生成json数据</el-button>
  <el-table
    :data="tableData"
    style="width: 100%">
    <el-table-column type="expand">
      <template slot-scope="props">
        <el-form label-position="left" inline class="demo-table-expand">
          <el-form-item label="系统名称">
            <span>{{ props.row.SystemName }}</span>
          </el-form-item>
          <el-form-item label="所属模块">
            <span>{{ props.row.Module }}</span>
          </el-form-item>
          <el-form-item label="操作时间">
            <span>{{ props.row.OpTime }}</span>
          </el-form-item>
          <el-form-item label="所属部门">
            <span>{{ props.row.OpDeptName }}</span>
          </el-form-item>
          <el-form-item label="操作类型">
            <span>{{ props.row.OpType }}</span>
          </el-form-item>
          <el-form-item label="客户端IP">
            <span>{{ props.row.ClientIp }}</span>
          </el-form-item>
          <el-form-item label="Mac地址">
            <span>{{ props.row.Mac }}</span>
          </el-form-item>
          <el-form-item label="HasAttach">
            <span>{{ props.row.HasAttach }}</span>
          </el-form-item>
          <el-form-item label="目标号码">
            <span>{{ props.row.TargetNum }}</span>
          </el-form-item>
          <el-form-item label="目标名称">
            <span>{{ props.row.TargetName }}</span>
          </el-form-item>
          <el-form-item label="任务名称">
            <span>{{ props.row.TaskName }}</span>
          </el-form-item>
          <el-form-item label="区域code">
            <span>{{ props.row.AreaCode }}</span>
          </el-form-item>
          <el-form-item label="WfApproveUserName">
            <span>{{ props.row.WfApproveUserName }}</span>
          </el-form-item>
          <el-form-item label="BusiId">
            <span>{{ props.row.BusiId }}</span>
          </el-form-item>
        </el-form>
      </template>
    </el-table-column>
    <el-table-column
      label="系统名称"
      prop="SystemName">
    </el-table-column>
    <el-table-column
      label="操作人员"
      prop="OpUserName">
    </el-table-column>
    <el-table-column
      label="描述"
      prop="OpDesc">
    </el-table-column>
  </el-table>
  <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page=currentPage
      :page-sizes="[10, 50, 100, 200]"
      :page-size=pageSize
      layout="total, sizes, prev, pager, next, jumper"
      :total=total>
    </el-pagination>

</div>
</template>

<script>
var FileSave = require('file-saver')
export default {
  name: 'HelloWorld',
  data () {
    return {
      resArr: [],
      tableData: [],
      total: 0,
      currentPage: 1,
      pageSize: 10
    }
  },
  methods: {
    beforeUpload: function (file) {
      const extension = file.name.split('.')[1] === 'txt'
      const isLt2M = file.size / 1024 / 1024 < 500
      if (!extension) {
        this.$notify({
          title: '警告',
          message: '文件格式必须为txt',
          type: 'warning'
        })
        return
      }
      if (!isLt2M) {
        this.$notify({
          title: '警告',
          message: '文件大小不能超过500M',
          type: 'warning'
        })
        return
      }
      var reader = new FileReader()// 这里是核心！！！读取操作就是由它完成的。
      reader.readAsText(file)// 读取文件的内容
      var _this = this
      reader.onload = function () {
        _this.resArr = this.result.split('\n')
        _this.total = _this.resArr.length > 1 ? _this.resArr.length - 1 : 0
        _this.pushData(_this)
      }
    },
    pushData (that) {
      if (that.resArr.length > 1) {
        let len = that.resArr.length - 1 > that.pageSize ? that.pageSize : that.resArr.length - 1
        for (let i = 0; i < len; i++) {
          that.tableData.push(JSON.parse(that.resArr[i]))
        }
      }
    },
    handleSizeChange (val) {
      console.log(`每页 ${val} 条`)
      this.pageSize = val
      this.tableData = []
      this.pushData(this)
    },
    handleCurrentChange (val) {
      console.log(`当前页: ${val}`)
      this.currentPage = val
      this.tableData = []
      let start = (this.currentPage - 1) * this.pageSize
      let end = this.resArr.length - 1 > this.currentPage * this.pageSize ? this.currentPage * this.pageSize : this.resArr.length - 1
      for (let i = start; i < end; i++) {
        this.tableData.push(JSON.parse(this.resArr[i]))
      }
    },
    expto () {
      let example = '{"MsgId": 1,"SystemName": 1,"Module": "fdd","OpTime": "aa","OpUserName": "aa","OpDeptName":"cc","OpType": "aa","OpDesc": "aa","ClientIp": "aa","Mac": "aa","HasAttach": "aa","TargetNum": "aa","TargetName": "aa","TaskName": "ss","AreaCode": "aa","WfApproveUserName": "aa","BusiId": "aa"}'
      let content = ''
      for (let i = 0; i < 2000000; i++) {
        content += example + '\n'
      }
      let blob = new Blob([content], {type: 'text/plain;charset=utf-8'})
      FileSave.saveAs(blob, 'file.txt')
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .basetable /deep/ .el-upload--text{
    width: 80px;
    height: 30px;
    }
  .demo-table-expand {
    font-size: 0;
  }
  .demo-table-expand label {
    width: 90px;
    color: #99a9bf;
  }
  .demo-table-expand .el-form-item {
    margin-right: 0;
    margin-bottom: 0;
    width: 50%;
  }

</style>
