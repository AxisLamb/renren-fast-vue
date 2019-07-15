<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="服务器名称" prop="serverName">
      <el-input v-model="dataForm.serverName" placeholder="服务器名称"></el-input>
    </el-form-item>
    <el-form-item label="服务器IP" prop="serverIp">
      <el-input v-model="dataForm.serverIp" placeholder="服务器IP"></el-input>
    </el-form-item>
    <el-form-item label="服务器端口" prop="serverPort">
      <el-input v-model="dataForm.serverPort" placeholder="服务器端口"></el-input>
    </el-form-item>
    <el-form-item label="服务器域名 www.baidu.com" prop="serverDomainName">
      <el-input v-model="dataForm.serverDomainName" placeholder="服务器域名 www.baidu.com"></el-input>
    </el-form-item>
    <el-form-item label="服务器访问协议 http  tcp/udp  rmi  rpc" prop="serverProtocol">
      <el-input v-model="dataForm.serverProtocol" placeholder="服务器访问协议 http  tcp/udp  rmi  rpc"></el-input>
    </el-form-item>
    <el-form-item label="服务器类型 1.公用    2. 私用" prop="serverType">
      <el-input v-model="dataForm.serverType" placeholder="服务器类型 1.公用    2. 私用"></el-input>
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
          serverName: '',
          serverIp: '',
          serverPort: '',
          serverDomainName: '',
          serverProtocol: '',
          serverType: '',
          revision: '',
          creator: '',
          createTime: '',
          modifier: ''
        },
        dataRule: {
          serverName: [
            { required: true, message: '服务器名称不能为空', trigger: 'blur' }
          ],
          serverIp: [
            { required: true, message: '服务器IP不能为空', trigger: 'blur' }
          ],
          serverPort: [
            { required: true, message: '服务器端口不能为空', trigger: 'blur' }
          ],
          serverDomainName: [
            { required: true, message: '服务器域名 www.baidu.com不能为空', trigger: 'blur' }
          ],
          serverProtocol: [
            { required: true, message: '服务器访问协议 http  tcp/udp  rmi  rpc不能为空', trigger: 'blur' }
          ],
          serverType: [
            { required: true, message: '服务器类型 1.公用    2. 私用不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/wms/serverinfo/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.serverName = data.serverinfo.serverName
                this.dataForm.serverIp = data.serverinfo.serverIp
                this.dataForm.serverPort = data.serverinfo.serverPort
                this.dataForm.serverDomainName = data.serverinfo.serverDomainName
                this.dataForm.serverProtocol = data.serverinfo.serverProtocol
                this.dataForm.serverType = data.serverinfo.serverType
                this.dataForm.revision = data.serverinfo.revision
                this.dataForm.creator = data.serverinfo.creator
                this.dataForm.createTime = data.serverinfo.createTime
                this.dataForm.modifier = data.serverinfo.modifier
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
              url: this.$http.adornUrl(`/wms/serverinfo/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'serverName': this.dataForm.serverName,
                'serverIp': this.dataForm.serverIp,
                'serverPort': this.dataForm.serverPort,
                'serverDomainName': this.dataForm.serverDomainName,
                'serverProtocol': this.dataForm.serverProtocol,
                'serverType': this.dataForm.serverType,
                'revision': this.dataForm.revision,
                'creator': this.dataForm.creator,
                'createTime': this.dataForm.createTime,
                'modifier': this.dataForm.modifier
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
