<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="商品名称" prop="name">
      <el-input v-model="dataForm.name" placeholder="商品名称"></el-input>
    </el-form-item>
    <el-form-item label="数量" prop="num">
      <!--<el-input v-model="dataForm.num" placeholder="数量"></el-input>-->
      <el-input-number v-model="dataForm.num" :min="1" label="数量"></el-input-number>
    </el-form-item>
    <el-form-item label="价格" prop="price">
      <el-input v-model="dataForm.price" placeholder="价格"></el-input>
    </el-form-item>
    <el-form-item label="规格" prop="specs">
      <el-input v-model="dataForm.specs" placeholder="规格"></el-input>
    </el-form-item>
    <el-form-item label="单价" prop="unitPrice">
      <el-input v-model="dataForm.unitPrice" placeholder="单价"></el-input>
    </el-form-item>
    <!--<el-form-item label="客户id" prop="customerId">
      <el-input v-model="dataForm.customerId" placeholder="客户id"></el-input>
    </el-form-item>-->
    <el-form-item label="客户名称" prop="customerName">
      <el-input v-model="dataForm.customerName" placeholder="客户名称"></el-input>
    </el-form-item>
    <el-form-item label="生产日期" prop="storageDate">
      <!--<el-input v-model="dataForm.storageDate" placeholder="生产日期"></el-input>-->
      <el-date-picker
        v-model="dataForm.storageDate"
        type="datetime"
        placeholder="选择生产日期"
        align="right"
        :picker-options="pickerOptions"
        value-format	="yyyy-MM-dd hh:mm:ss">
      </el-date-picker>
    </el-form-item>

    <!--<el-form-item label="创建时间" prop="createDate">
      <el-input v-model="dataForm.createDate" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="创建人" prop="createBy">
      <el-input v-model="dataForm.createBy" placeholder="创建人"></el-input>
    </el-form-item>
    <el-form-item label="修改人" prop="updateBy">
      <el-input v-model="dataForm.updateBy" placeholder=""></el-input>
    </el-form-item>
    <el-form-item label="修改时间" prop="updateDate">
      <el-input v-model="dataForm.updateDate" placeholder=""></el-input>
    </el-form-item>-->
    <el-form-item label="商品分类" prop="itemizeName">
      <!--<el-input v-model="dataForm.itemizeId" placeholder="商品分类"></el-input>-->
      <el-select v-model="dataForm.itemizeName"  clearable placeholder="请选择商品分类">
        <el-option
          v-for="item in itemizeList"
          :key="item.id"
          :label="item.name"
          :value="item.id">
        </el-option>
      </el-select>
    </el-form-item>

    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  import { isZheng } from '@/utils/validate'
  export default {
    data () {
      var validatePrice = (rule, value, callback) => {
        if (!isZheng(value)) {
          callback(new Error('价格不能为非数字'))
        } else {
          callback()
        }
      }
      var validateUnitPrice = (rule, value, callback) => {
        if (!isZheng(value)) {
          callback(new Error('单价不能为非数字'))
        } else {
          callback()
        }
      }
      return {
        visible: false,
        dataForm: {
          id: 0,
          name: '',
          num: '',
          price: '',
          specs: '',
          unitPrice: '',
          customerId: '',
          customerName: '',
          storageDate: '',
          createDate: '',
          createBy: '',
          updateBy: '',
          updateDate: '',
          itemizeId: '',
          itemizeName: ''
        },
        itemizeList: [],
        dataRule: {
          name: [
            { required: true, message: '商品名称不能为空', trigger: 'blur' }
          ],
          num: [
            { required: true, message: '数量不能为空', trigger: 'blur' }
          ],
          price: [
            { required: true, message: '价格不能为空', trigger: 'blur' },
            { validator: validatePrice, trigger: 'blur' }
          ],
          specs: [
            { required: true, message: '规格不能为空', trigger: 'blur' }
          ],
          unitPrice: [
            { required: true, message: '单价不能为空', trigger: 'blur' },
            { validator: validateUnitPrice, trigger: 'blur' }
          ],
          customerName: [
            { required: true, message: '客户名称不能为空', trigger: 'blur' }
          ],
          storageDate: [
            { required: true, message: '生产日期不能为空', trigger: 'blur' }
          ],
          itemizeName: [
            { required: true, message: '商品分类不能为空', trigger: 'blur' }
          ]
        },
        pickerOptions: {
          shortcuts: [{
            text: '今天',
            onClick (picker) {
              picker.$emit('pick', new Date())
            }
          }, {
            text: '昨天',
            onClick (picker) {
              const date = new Date()
              date.setTime(date.getTime() - 3600 * 1000 * 24)
              picker.$emit('pick', date)
            }
          }, {
            text: '一周前',
            onClick (picker) {
              const date = new Date()
              date.setTime(date.getTime() - 3600 * 1000 * 24 * 7)
              picker.$emit('pick', date)
            }
          }]
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
              url: this.$http.adornUrl(`/goods/goods/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.name = data.goods.name
                this.dataForm.num = data.goods.num
                this.dataForm.price = data.goods.price
                this.dataForm.specs = data.goods.specs
                this.dataForm.unitPrice = data.goods.unitPrice
                this.dataForm.customerId = data.goods.customerId
                this.dataForm.customerName = data.goods.customerName
                this.dataForm.storageDate = data.goods.storageDate
                this.dataForm.createDate = data.goods.createDate
                this.dataForm.createBy = data.goods.createBy
                this.dataForm.updateBy = data.goods.updateBy
                this.dataForm.updateDate = data.goods.updateDate
                this.dataForm.itemizeId = data.goods.itemizeId
                this.dataForm.itemizeName = data.itemize.name
                this.itemizeList = data.itemizeList
              }
            })
          } else {
            this.$http({
              url: this.$http.adornUrl(`/goods/goods/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.itemizeList = data.itemizeList
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
              url: this.$http.adornUrl(`/goods/goods/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'name': this.dataForm.name,
                'num': this.dataForm.num,
                'price': this.dataForm.price,
                'specs': this.dataForm.specs,
                'unitPrice': this.dataForm.unitPrice,
                'customerId': this.dataForm.customerId,
                'customerName': this.dataForm.customerName,
                'storageDate': this.dataForm.storageDate,
                'createDate': this.dataForm.createDate,
                'createBy': this.dataForm.createBy,
                'updateBy': this.dataForm.updateBy,
                'updateDate': this.dataForm.updateDate,
                'itemizeId': this.dataForm.itemizeName
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
