<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.key" placeholder="参数名" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="getDataList()">查询</el-button>
        <!-- <el-button v-if="isAuth('wms:weibocontent:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button> -->
        <!-- <el-button v-if="isAuth('wms:weibocontent:delete')" type="danger" @click="deleteHandle()" :disabled="dataListSelections.length <= 0">批量删除</el-button> -->
        <!-- <el-button type="danger" @click="genFiles()" :disabled="dataListSelections.length <= 0">生成模板</el-button> -->
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      :row-key="getRowKeys"
      border
      v-loading="dataListLoading"
      @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column
        type="selection"
        header-align="center"
        align="center"
        :reserve-selection="true"
        width="50">
      </el-table-column>
      <el-table-column
        prop="id"
        header-align="center"
        align="center"
        label="主键">
      </el-table-column>
      <el-table-column
        prop="weiboContent"
        header-align="center"
        align="center"
        label="微博内容">
      </el-table-column>
      <el-table-column
        prop="weiboTime"
        header-align="center"
        align="center"
        label="发布时间">
      </el-table-column>
      <!-- <el-table-column
        prop="weiboProfileId"
        header-align="center"
        align="center"
        label="微博人信息id"> -->
      </el-table-column>
      <el-table-column
        prop="commentsCount"
        header-align="center"
        align="center"
        label="微博评论数">
      </el-table-column>
      <el-table-column
        prop="repostsCount"
        header-align="center"
        align="center"
        label="微博转发数">
      </el-table-column>
      <el-table-column
        prop="attitudesCount"
        header-align="center"
        align="center"
        label="微博点赞数">
      </el-table-column>
      <!-- <el-table-column
        prop="authorName"
        header-align="center"
        align="center"
        label="微博作者昵称">
      </el-table-column> -->
      <el-table-column
        prop="profileUrl"
        header-align="center"
        align="center"
        label="微博地址首页">
      </el-table-column>
      <!-- <el-table-column
        prop="weibo_picture"
        header-align="center"
        align="center"
        label="微博图片">
        <template slot-scope="scope">
          <el-popover
                placement="right"
                title=""
                trigger="click">
            <img slot="reference" v-for="item in scope.row.picPathList" :src="item" :alt="item" style="max-height: 100px;max-width: 100px"/>
            <img v-for="item in scope.row.picPathList" :src="item" width="600" height="600" class="head_pic"/>
          </el-popover>
        </template>
      </el-table-column> -->
      <el-table-column
        prop="weibo_picture"
        header-align="center"
        align="center"
        label="微博图片">
        <template slot-scope="scope">
          <el-button v-if="scope.row.picPathList.length > 0" :data-img="scope.row.picPathList" type="text" size="small" @click="$imgPreview">查看图片</el-button>
        </template>
      </el-table-column>
      <!-- <el-table-column
        prop="authorDesc"
        header-align="center"
        align="center"
        label="微博博主个人说明">
      </el-table-column>
      <el-table-column
        prop="authorFollow"
        header-align="center"
        align="center"
        label="博主关注人数">
      </el-table-column>
      <el-table-column
        prop="authorFans"
        header-align="center"
        align="center"
        label="博主粉丝数">
      </el-table-column> -->
      <el-table-column
        prop="createTime"
        header-align="center"
        align="center"
        label="创建时间">
      </el-table-column>
      <el-table-column
        prop="modifiedTime"
        header-align="center"
        align="center"
        label="更新时间">
      </el-table-column>
      <el-table-column
        fixed="right"
        header-align="center"
        align="center"
        width="150"
        label="操作">
        <template slot-scope="scope">
          <!-- <el-button type="text" size="small" @click="addOrUpdateHandle(scope.row.id)">修改</el-button> -->
          <el-button type="text" size="small" @click="deleteHandle(scope.row.id)">删除</el-button>
        </template>
      </el-table-column>
    </el-table>
    <el-pagination
      @size-change="sizeChangeHandle"
      @current-change="currentChangeHandle"
      :current-page="pageIndex"
      :page-sizes="[10, 20, 50, 100]"
      :page-size="pageSize"
      :total="totalPage"
      layout="total, sizes, prev, pager, next, jumper">
    </el-pagination>
    <!-- 弹窗, 新增 / 修改 -->
    <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update>
  </div>
</template>

<script>
  import AddOrUpdate from './weibocontent-add-or-update'
  export default {
    data () {
      return {
        dataForm: {
          key: ''
        },
        dataList: [],
        pageIndex: 1,
        pageSize: 10,
        totalPage: 0,
        dataListLoading: false,
        dataListSelections: [],
        // selectDataObj: {},
        addOrUpdateVisible: false
      }
    },
    watch: {
      dataListSelections: function(val, oldVal){
        this.genContent(val)
      }
    },
    components: {
      AddOrUpdate
    },
    activated () {
      this.getDataList()
    },
    methods: {
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/wms/weibocontent/funList'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'weiboContent': this.dataForm.key
          })
        }).then(({data}) => {
          if (data && data.code === 0) {
            this.dataList = data.page.list
            this.totalPage = data.page.totalCount
          } else {
            this.dataList = []
            this.totalPage = 0
          }
          this.dataListLoading = false
        })
      },
      // 每页数
      sizeChangeHandle (val) {
        this.pageSize = val
        this.pageIndex = 1
        this.getDataList()
      },
      // 当前页
      currentChangeHandle (val) {
        this.pageIndex = val
        this.getDataList()
      },
      // 多选
      selectionChangeHandle (val) {
        this.dataListSelections = val
      },
      // 新增 / 修改
      addOrUpdateHandle (id) {
        this.addOrUpdateVisible = true
        this.$nextTick(() => {
          this.$refs.addOrUpdate.init(id)
        })
      },
      // 删除
      deleteHandle (id) {
        var ids = id ? [id] : this.dataListSelections.map(item => {
          return item.id
        })
        this.$confirm(`确定对[id=${ids.join(',')}]进行[${id ? '删除' : '批量删除'}]操作?`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$http({
            url: this.$http.adornUrl('/wms/weibocontent/delete'),
            method: 'post',
            data: this.$http.adornData(ids, false)
          }).then(({data}) => {
            if (data && data.code === 0) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500,
                onClose: () => {
                  this.getDataList()
                }
              })
            } else {
              this.$message.error(data.msg)
            }
          })
        })
      },

      // 根据勾选生成微博html
      // genFiles () {
      //   var ids = this.dataListSelections.map(item => {
      //     return item.id
      //   })
      //   location.href = "http://47.107.248.132/renren-fast/wms/weibocontent/genFile?ids=" + ids;
      // }, 

      // 根据val生成模板内容
      genContent(val) {
        var jsonRes = {}
        var idArr = []
        // 将weibocontent页面所勾选内容加入到缓存中
        val.forEach(item => {
          // push id
          var weiboId = item.id
          idArr.push(weiboId)
          var weiboContent = item.weiboContent
          var picPathList = item.picPathList
          if(picPathList != null){
              jsonRes[weiboContent] = picPathList.map(pl => {return "<div style='margin: 10px'><img src='" + pl + "'/></div>"}).reduce((x, y) => x + y)
          } else {
            jsonRes[weiboContent] = ""
          }
        })
        
        // 更新缓存
        Lockr.set('funJson', jsonRes)
        // 更新id
        Lockr.set('selectFunId', idArr)
      }, 

      getRowKeys(row){
         return row.id
      }

    }
  }
</script>
