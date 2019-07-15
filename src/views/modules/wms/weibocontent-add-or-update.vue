<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="微博内容" prop="weiboContent">
      <el-input v-model="dataForm.weiboContent" placeholder="微博内容"></el-input>
    </el-form-item>
    <el-form-item label="发布时间" prop="weiboTime">
      <el-input v-model="dataForm.weiboTime" placeholder="发布时间"></el-input>
    </el-form-item>
    <el-form-item label="微博人信息id" prop="weiboProfileId">
      <el-input v-model="dataForm.weiboProfileId" placeholder="微博人信息id"></el-input>
    </el-form-item>
    <el-form-item label="微博评论数" prop="commentsCount">
      <el-input v-model="dataForm.commentsCount" placeholder="微博评论数"></el-input>
    </el-form-item>
    <el-form-item label="微博转发数" prop="repostsCount">
      <el-input v-model="dataForm.repostsCount" placeholder="微博转发数"></el-input>
    </el-form-item>
    <el-form-item label="微博点赞数" prop="attitudesCount">
      <el-input v-model="dataForm.attitudesCount" placeholder="微博点赞数"></el-input>
    </el-form-item>
    <el-form-item label="微博作者昵称" prop="authorName">
      <el-input v-model="dataForm.authorName" placeholder="微博作者昵称"></el-input>
    </el-form-item>
    <el-form-item label="微博地址首页" prop="profileUrl">
      <el-input v-model="dataForm.profileUrl" placeholder="微博地址首页"></el-input>
    </el-form-item>
    <el-form-item label="微博博主个人说明" prop="authorDesc">
      <el-input v-model="dataForm.authorDesc" placeholder="微博博主个人说明"></el-input>
    </el-form-item>
    <el-form-item label="博主关注人数" prop="authorFollow">
      <el-input v-model="dataForm.authorFollow" placeholder="博主关注人数"></el-input>
    </el-form-item>
    <el-form-item label="博主粉丝数" prop="authorFans">
      <el-input v-model="dataForm.authorFans" placeholder="博主粉丝数"></el-input>
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
          weiboContent: '',
          weiboTime: '',
          weiboProfileId: '',
          commentsCount: '',
          repostsCount: '',
          attitudesCount: '',
          authorName: '',
          profileUrl: '',
          authorDesc: '',
          authorFollow: '',
          authorFans: '',
          revision: '',
          creator: '',
          createTime: '',
          modifier: '',
          modifiedTime: ''
        },
        dataRule: {
          weiboContent: [
            { required: true, message: '微博内容不能为空', trigger: 'blur' }
          ],
          weiboTime: [
            { required: true, message: '发布时间不能为空', trigger: 'blur' }
          ],
          weiboProfileId: [
            { required: true, message: '微博人信息id不能为空', trigger: 'blur' }
          ],
          commentsCount: [
            { required: true, message: '微博评论数不能为空', trigger: 'blur' }
          ],
          repostsCount: [
            { required: true, message: '微博转发数不能为空', trigger: 'blur' }
          ],
          attitudesCount: [
            { required: true, message: '微博点赞数不能为空', trigger: 'blur' }
          ],
          authorName: [
            { required: true, message: '微博作者昵称不能为空', trigger: 'blur' }
          ],
          profileUrl: [
            { required: true, message: '微博地址首页不能为空', trigger: 'blur' }
          ],
          authorDesc: [
            { required: true, message: '微博博主个人说明不能为空', trigger: 'blur' }
          ],
          authorFollow: [
            { required: true, message: '博主关注人数不能为空', trigger: 'blur' }
          ],
          authorFans: [
            { required: true, message: '博主粉丝数不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/wms/weibocontent/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.weiboContent = data.weibocontent.weiboContent
                this.dataForm.weiboTime = data.weibocontent.weiboTime
                this.dataForm.weiboProfileId = data.weibocontent.weiboProfileId
                this.dataForm.commentsCount = data.weibocontent.commentsCount
                this.dataForm.repostsCount = data.weibocontent.repostsCount
                this.dataForm.attitudesCount = data.weibocontent.attitudesCount
                this.dataForm.authorName = data.weibocontent.authorName
                this.dataForm.profileUrl = data.weibocontent.profileUrl
                this.dataForm.authorDesc = data.weibocontent.authorDesc
                this.dataForm.authorFollow = data.weibocontent.authorFollow
                this.dataForm.authorFans = data.weibocontent.authorFans
                this.dataForm.revision = data.weibocontent.revision
                this.dataForm.creator = data.weibocontent.creator
                this.dataForm.createTime = data.weibocontent.createTime
                this.dataForm.modifier = data.weibocontent.modifier
                this.dataForm.modifiedTime = data.weibocontent.modifiedTime
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
              url: this.$http.adornUrl(`/wms/weibocontent/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'weiboContent': this.dataForm.weiboContent,
                'weiboTime': this.dataForm.weiboTime,
                'weiboProfileId': this.dataForm.weiboProfileId,
                'commentsCount': this.dataForm.commentsCount,
                'repostsCount': this.dataForm.repostsCount,
                'attitudesCount': this.dataForm.attitudesCount,
                'authorName': this.dataForm.authorName,
                'profileUrl': this.dataForm.profileUrl,
                'authorDesc': this.dataForm.authorDesc,
                'authorFollow': this.dataForm.authorFollow,
                'authorFans': this.dataForm.authorFans,
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
