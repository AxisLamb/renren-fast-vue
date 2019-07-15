<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="图片分组code" prop="picGroupCode">
      <el-input v-model="dataForm.picGroupCode" placeholder="图片分组code"></el-input>
    </el-form-item>
    <el-form-item label="图片相对路径" prop="picRelativePath">
      <el-input v-model="dataForm.picRelativePath" placeholder="图片相对路径"></el-input>
    </el-form-item>
    <el-form-item label="图片名称" prop="picName">
      <el-input v-model="dataForm.picName" placeholder="图片名称"></el-input>
    </el-form-item>
    <el-form-item label="图片类型" prop="picType">
      <el-input v-model="dataForm.picType" placeholder="图片类型"></el-input>
    </el-form-item>
    <el-form-item label="http访问地址" prop="httpUrl">
      <el-input v-model="dataForm.httpUrl" placeholder="http访问地址"></el-input>
    </el-form-item>
    <el-form-item label="访问token" prop="picToken">
      <el-input v-model="dataForm.picToken" placeholder="访问token"></el-input>
    </el-form-item>
    <el-form-item label="是否有效" prop="picEnable">
      <el-input v-model="dataForm.picEnable" placeholder="是否有效"></el-input>
    </el-form-item>
    <el-form-item label="乐观锁" prop="revision">
      <el-input v-model="dataForm.revision" placeholder="乐观锁"></el-input>
    </el-form-item>
    <el-form-item label="创建人" prop="creator">
      <el-input v-model="dataForm.creator" placeholder="创建人"></el-input>
    </el-form-item>
    <el-form-item label="创建时间" prop="createTime">
      <el-input v-model="dataForm.createTime" placeholder="创建时间"></el-input>
    </el-form-item>
    <el-form-item label="更新人" prop="modifier">
      <el-input v-model="dataForm.modifier" placeholder="更新人"></el-input>
    </el-form-item>
    <el-form-item label="更新时间" prop="modifiedTime">
      <el-input v-model="dataForm.modifiedTime" placeholder="更新时间"></el-input>
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
          picGroupCode: '',
          picRelativePath: '',
          picName: '',
          picType: '',
          httpUrl: '',
          picToken: '',
          picEnable: '',
          revision: '',
          creator: '',
          createTime: '',
          modifier: '',
          modifiedTime: ''
        },
        dataRule: {
          picGroupCode: [
            { required: true, message: '图片分组code不能为空', trigger: 'blur' }
          ],
          picRelativePath: [
            { required: true, message: '图片相对路径不能为空', trigger: 'blur' }
          ],
          picName: [
            { required: true, message: '图片名称不能为空', trigger: 'blur' }
          ],
          picType: [
            { required: true, message: '图片类型不能为空', trigger: 'blur' }
          ],
          httpUrl: [
            { required: true, message: 'http访问地址不能为空', trigger: 'blur' }
          ],
          picToken: [
            { required: true, message: '访问token不能为空', trigger: 'blur' }
          ],
          picEnable: [
            { required: true, message: '是否有效不能为空', trigger: 'blur' }
          ],
          revision: [
            { required: true, message: '乐观锁不能为空', trigger: 'blur' }
          ],
          creator: [
            { required: true, message: '创建人不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '创建时间不能为空', trigger: 'blur' }
          ],
          modifier: [
            { required: true, message: '更新人不能为空', trigger: 'blur' }
          ],
          modifiedTime: [
            { required: true, message: '更新时间不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/wms/pictureinfo/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.picGroupCode = data.pictureinfo.picGroupCode
                this.dataForm.picRelativePath = data.pictureinfo.picRelativePath
                this.dataForm.picName = data.pictureinfo.picName
                this.dataForm.picType = data.pictureinfo.picType
                this.dataForm.httpUrl = data.pictureinfo.httpUrl
                this.dataForm.picToken = data.pictureinfo.picToken
                this.dataForm.picEnable = data.pictureinfo.picEnable
                this.dataForm.revision = data.pictureinfo.revision
                this.dataForm.creator = data.pictureinfo.creator
                this.dataForm.createTime = data.pictureinfo.createTime
                this.dataForm.modifier = data.pictureinfo.modifier
                this.dataForm.modifiedTime = data.pictureinfo.modifiedTime
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
              url: this.$http.adornUrl(`/wms/pictureinfo/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'picGroupCode': this.dataForm.picGroupCode,
                'picRelativePath': this.dataForm.picRelativePath,
                'picName': this.dataForm.picName,
                'picType': this.dataForm.picType,
                'httpUrl': this.dataForm.httpUrl,
                'picToken': this.dataForm.picToken,
                'picEnable': this.dataForm.picEnable,
                'revision': this.dataForm.revision,
                'creator': this.dataForm.creator,
                'createTime': this.dataForm.createTime,
                'modifier': this.dataForm.modifier,
                'modifiedTime': this.dataForm.modifiedTime
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
