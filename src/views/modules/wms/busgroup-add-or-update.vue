<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="分组code" prop="groupCode">
      <el-input v-model="dataForm.groupCode" placeholder="分组code"></el-input>
    </el-form-item>
    <el-form-item label="组别描述" prop="desc">
      <el-input v-model="dataForm.desc" placeholder="组别描述"></el-input>
    </el-form-item>
    <el-form-item label="组别type 比如dict，pic" prop="type">
      <el-input v-model="dataForm.type" placeholder="组别type 比如dict，pic"></el-input>
    </el-form-item>
    <el-form-item label="所属项目名称" prop="projectName">
      <el-input v-model="dataForm.projectName" placeholder="所属项目名称"></el-input>
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
          groupCode: '',
          desc: '',
          type: '',
          projectName: '',
          revision: '',
          creator: '',
          createTime: '',
          modifier: '',
          modifiedTime: ''
        },
        dataRule: {
          groupCode: [
            { required: true, message: '分组code不能为空', trigger: 'blur' }
          ],
          desc: [
            { required: true, message: '组别描述不能为空', trigger: 'blur' }
          ],
          type: [
            { required: true, message: '组别type 比如dict，pic不能为空', trigger: 'blur' }
          ],
          projectName: [
            { required: true, message: '所属项目名称不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/wms/busgroup/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.groupCode = data.busgroup.groupCode
                this.dataForm.desc = data.busgroup.desc
                this.dataForm.type = data.busgroup.type
                this.dataForm.projectName = data.busgroup.projectName
                this.dataForm.revision = data.busgroup.revision
                this.dataForm.creator = data.busgroup.creator
                this.dataForm.createTime = data.busgroup.createTime
                this.dataForm.modifier = data.busgroup.modifier
                this.dataForm.modifiedTime = data.busgroup.modifiedTime
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
              url: this.$http.adornUrl(`/wms/busgroup/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'groupCode': this.dataForm.groupCode,
                'desc': this.dataForm.desc,
                'type': this.dataForm.type,
                'projectName': this.dataForm.projectName,
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
