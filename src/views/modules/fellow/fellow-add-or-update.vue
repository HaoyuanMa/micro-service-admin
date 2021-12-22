<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="用户名" prop="username">
      <el-input v-model="dataForm.username" placeholder="用户名" :disabled="!editable"></el-input>
    </el-form-item>
    <el-form-item label="手机号码" prop="mobile">
      <el-input v-model="dataForm.mobile" placeholder="手机号码"></el-input>
    </el-form-item>
    <el-form-item label="邮箱" prop="email">
      <el-input v-model="dataForm.email" placeholder="邮箱"></el-input>
    </el-form-item>
    <el-form-item label="性别" prop="gender">
      <el-dropdown trigger="hover" @command="setGender" style="width: 10%">
        <span class="el-dropdown-link">
          <el-input v-model="dataForm.gender" placeholder="性别"></el-input>
        </span>
        <el-dropdown-menu slot="dropdown">
          <el-dropdown-item command="1">男</el-dropdown-item>
          <el-dropdown-item command="0">女</el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>
    </el-form-item>
    <el-form-item label="头像">
      <template slot-scope="scope" id="imageUpload">
        <VueCoreImageUpload
          style="
          border-radius: 4px;
          border: 1px solid #dcdfe6;"
          class="btn btn-primary"
          crop="local"
          cropRatio="1:1"
          compress="80"
          @imagechanged="imageChanged"
          :isXhr="false"
          :max-file-size="5242880"
          :credentials="false"
          inputAccept="image/*"
          inputOfFile="file"
          ref="upImg"
          :text="btnTitle"
        >
        </VueCoreImageUpload>
      </template>
    </el-form-item>
    <el-form-item label="定价" prop="price">
      <el-input v-model="dataForm.price" placeholder="定价"></el-input>
    </el-form-item>
    <el-form-item label="年龄" prop="age">
      <el-input v-model="dataForm.age" placeholder="年龄"></el-input>
    </el-form-item>
    <el-form-item label="省" prop="province">
      <el-input v-model="dataForm.province" placeholder="省"></el-input>
    </el-form-item>
    <el-form-item label="市" prop="city">
      <el-input v-model="dataForm.city" placeholder="所在城市"></el-input>
    </el-form-item>
    <el-form-item label="区" prop="region">
      <el-input v-model="dataForm.region" placeholder="区"></el-input>
    </el-form-item>
    <el-form-item label="详细地址" prop="detailAddress">
      <el-input v-model="dataForm.detailAddress" placeholder="详细地址"></el-input>
    </el-form-item>
    <el-form-item label="个性签名" prop="sign">
      <el-input v-model="dataForm.sign" placeholder="个性签名"></el-input>
    </el-form-item>
    <el-form-item label="简介" prop="description">
      <el-input v-model="dataForm.description" placeholder="简介"></el-input>
    </el-form-item>
    <el-form-item label="状态" prop="status">
      <el-input v-model="dataForm.status" placeholder="状态"></el-input>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  import VueCoreImageUpload from 'vue-core-image-upload'
  export default {
    components: {
      VueCoreImageUpload
    },
    data () {
      return {
        btnTitle: '上传头像',
        visible: false,
        editable: true,
        dataForm: {
          id: 0,
          username: '',
          mobile: '',
          email: '',
          header: '',
          gender: '',
          age: '',
          province: '',
          city: '',
          region: '',
          detailAddress: '',
          sign: '',
          description: '',
          status: '',
          price: 0.0
        },
        dataRule: {
          username: [
            { required: true, message: '用户名不能为空', trigger: 'blur' }
          ],
          mobile: [
            { required: true, message: '手机号码不能为空', trigger: 'blur' }
          ],
          email: [
            { required: true, message: '邮箱不能为空', trigger: 'blur' }
          ],
          gender: [
            { required: true, message: '性别不能为空', trigger: 'blur' }
          ],
          age: [
            { required: true, message: '年龄不能为空', trigger: 'blur' }
          ],
          province: [
            { required: true, message: '省不能为空', trigger: 'blur' }
          ],
          city: [
            { required: true, message: '所在城市不能为空', trigger: 'blur' }
          ],
          region: [
            { required: true, message: '区不能为空', trigger: 'blur' }
          ],
          detailAddress: [
            { required: true, message: '详细地址不能为空', trigger: 'blur' }
          ],
          status: [
            { required: true, message: '状态不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      setGender (command) {
        this.dataForm.gender = command
      },
      getGender: g => {
        return g === 1 ? '男' : '女'
      },
      imageChanged (src) {
        this.dataForm.header = src
        this.btnTitle = '已选择'
      },
      init (id) {
        this.editable = true
        this.dataForm.id = id || 0
        if (id !== 0) {
          this.editable = false
        }
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/fellow/fellow/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.username = data.fellow.username
                this.dataForm.mobile = data.fellow.mobile
                this.dataForm.email = data.fellow.email
                this.dataForm.header = data.fellow.header
                this.dataForm.price = data.fellow.price
                this.dataForm.gender = data.fellow.gender
                this.dataForm.age = data.fellow.age
                this.dataForm.province = data.fellow.province
                this.dataForm.city = data.fellow.city
                this.dataForm.region = data.fellow.region
                this.dataForm.detailAddress = data.fellow.detailAddress
                this.dataForm.sign = data.fellow.sign
                this.dataForm.description = data.fellow.description
                this.dataForm.status = data.fellow.status
                this.dataForm.createTime = data.fellow.createTime
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
              url: this.$http.adornUrl(`/fellow/fellow/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'username': this.dataForm.username,
                'mobile': this.dataForm.mobile,
                'email': this.dataForm.email,
                'header': this.dataForm.header,
                'gender': this.dataForm.gender,
                'price': this.dataForm.price,
                'age': this.dataForm.age,
                'province': this.dataForm.province,
                'city': this.dataForm.city,
                'region': this.dataForm.region,
                'detailAddress': this.dataForm.detailAddress,
                'sign': this.dataForm.sign,
                'description': this.dataForm.description,
                'status': this.dataForm.status
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

<style>
  #imageUpload {
    border: 1px solid;
  }
</style>
