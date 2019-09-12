<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="分类名称" prop="name">
      <el-input v-model="dataForm.name" placeholder="分类名称"></el-input>
    </el-form-item>
    <el-form-item label="备注" prop="remark">
      <el-input v-model="dataForm.remark" placeholder="备注"></el-input>
    </el-form-item>
      <!--<el-form-item label="创建人" prop="createBy">
        <el-input v-model="dataForm.createBy" placeholder="创建人">{{ userName }}</el-input>
      </el-form-item>
      <el-form-item label="创建日期" prop="createDate">
        <el-input v-model="dataForm.createDate" placeholder="创建日期"></el-input>
      </el-form-item>
      <el-form-item label="修改人" prop="updateBy">
        <el-input v-model="dataForm.updateBy" placeholder="修改人"></el-input>
      </el-form-item>
      <el-form-item label="修改日期" prop="updateDate">
        <el-input v-model="dataForm.updateDate" placeholder="修改日期"></el-input>
      </el-form-item>-->
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          id: 0,
          name: '',
          remark: '',
          createBy: '',
          createDate: '',
          updateBy: '',
          updateDate: ''
        },
        dataRule: {
          name: [
            { required: true, message: '分类名称不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/itemize/itemize/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.name = data.itemize.name
                this.dataForm.remark = data.itemize.remark
                this.dataForm.createBy = data.itemize.createBy
                this.dataForm.createDate = data.itemize.createDate
                this.dataForm.updateBy = data.itemize.updateBy
                this.dataForm.updateDate = data.itemize.updateDate
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/itemize/itemize/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'name': this.dataForm.name,
                'remark': this.dataForm.remark,
                'createBy': this.dataForm.createBy,
                'createDate': this.dataForm.createDate,
                'updateBy': this.dataForm.updateBy,
                'updateDate': this.dataForm.updateDate
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      }
    }
  }
</script>
