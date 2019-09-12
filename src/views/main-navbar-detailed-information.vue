<template>
  <el-dialog
    title="账号信息"
    :visible.sync="visible"
    :append-to-body="true">
    <el-form label-width="80px" >
      <el-form-item label="账号">
        <span>{{ userName }}</span>
      </el-form-item>
      <el-form-item label="手机号码">
        <span>{{ dataForm.mobile }}</span>
      </el-form-item>
      <el-form-item label="邮箱">
        <span>{{ dataForm.email }}</span>
      </el-form-item>
      <el-form-item label="状态">
        <span v-if="dataForm.status === 0">禁用</span>
        <span v-if="dataForm.status === 1">正常</span>
      </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
    </span>
  </el-dialog>
</template>
<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          email: '',
          mobile: '',
          status: ''
        }
      }
    },
    computed: {
      userName: {
        get () { return this.$store.state.user.name }
      },
      mainTabs: {
        get () { return this.$store.state.common.mainTabs },
        set (val) { this.$store.commit('common/updateMainTabs', val) }
      }
    },
    methods: {
      // 初始化
      init () {
        this.visible = true
        this.$nextTick(() => {
          this.$http({
            url: this.$http.adornUrl('/sys/user/info'),
            method: 'get',
            params: this.$http.adornParams()
          }).then(({data}) => {
            if (data && data.code === 0) {
              this.dataForm.email = data.user.email
              this.dataForm.mobile = data.user.mobile
              this.dataForm.status = data.user.status
            }
          })
        })
      }
    }
  }
</script>
