<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="客户名称" prop="name">
      <el-input v-model="dataForm.name" placeholder="客户名称"></el-input>
    </el-form-item>
    <el-form-item label="客户地址" prop="address">
      <el-input v-model="dataForm.address" placeholder="客户地址"></el-input>
    </el-form-item>
    <el-form-item label="联系方式" prop="phone">
      <el-input v-model="dataForm.phone" placeholder="联系方式"></el-input>
    </el-form-item>
    <el-form-item label="联系人" prop="contacts">
      <el-input v-model="dataForm.contacts" placeholder="联系人"></el-input>
    </el-form-item>
    <!--<el-form-item label="创建人" prop="createBy">
      <el-input v-model="dataForm.createBy" placeholder="创建人"></el-input>
    </el-form-item>
    <el-form-item label="创建时间" prop="createDate">
      <el-input v-model="dataForm.createDate" placeholder="创建时间"></el-input>
    </el-form-item>
    <el-form-item label="修改人" prop="updateBy">
      <el-input v-model="dataForm.updateBy" placeholder="修改人"></el-input>
    </el-form-item>
    <el-form-item label="修改时间" prop="updateDate">
      <el-input v-model="dataForm.updateDate" placeholder="修改时间"></el-input>
    </el-form-item>-->
    <el-form-item label="客户分类" prop="itemize">
      <!--<el-input v-model="dataForm.itemize" placeholder="客户分类"></el-input>-->
      <el-radio v-model="dataForm.itemize" :label="1" border size="medium">进货商</el-radio>
      <el-radio v-model="dataForm.itemize" :label="2" border size="medium">出货商</el-radio>
    </el-form-item>
    <el-form-item label="备注" prop="remark">
      <el-input v-model="dataForm.remark" placeholder="备注"></el-input>
    </el-form-item>
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
          address: '',
          phone: '',
          contacts: '',
          createBy: '',
          createDate: '',
          updateBy: '',
          updateDate: '',
          itemize: '',
          remark: ''
        },
        dataRule: {
          name: [
            { required: true, message: '客户名称不能为空', trigger: 'blur' }
          ],
          address: [
            { required: true, message: '客户地址不能为空', trigger: 'blur' }
          ],
          phone: [
            { required: true, message: '联系方式不能为空', trigger: 'blur' }
          ],
          contacts: [
            { required: true, message: '联系人不能为空', trigger: 'blur' }
          ],
          itemize: [
            { required: true, message: '客户分类不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/customer/customer/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.name = data.customer.name
                this.dataForm.address = data.customer.address
                this.dataForm.phone = data.customer.phone
                this.dataForm.contacts = data.customer.contacts
                this.dataForm.createBy = data.customer.createBy
                this.dataForm.createDate = data.customer.createDate
                this.dataForm.updateBy = data.customer.updateBy
                this.dataForm.updateDate = data.customer.updateDate
                this.dataForm.itemize = data.customer.itemize
                this.dataForm.remark = data.customer.remark
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
              url: this.$http.adornUrl(`/customer/customer/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'name': this.dataForm.name,
                'address': this.dataForm.address,
                'phone': this.dataForm.phone,
                'contacts': this.dataForm.contacts,
                'createBy': this.dataForm.createBy,
                'createDate': this.dataForm.createDate,
                'updateBy': this.dataForm.updateBy,
                'updateDate': this.dataForm.updateDate,
                'itemize': this.dataForm.itemize,
                'remark': this.dataForm.remark
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
