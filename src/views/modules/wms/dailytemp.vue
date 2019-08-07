<template>
  <div class="mod-config">
    <el-form :inline="true" :model="dataForm" @keyup.enter.native="getDataList()">
      <el-form-item>
        <el-input v-model="dataForm.key" placeholder="参数名" clearable></el-input>
      </el-form-item>
      <el-form-item>
        <el-button @click="reGenTemp()">重新生成模板</el-button>
        <!-- <el-button v-if="isAuth('wms:dailytemp:save')" type="primary" @click="addOrUpdateHandle()">新增</el-button> -->
        <el-button v-if="isAuth('wms:dailytemp:delete')" type="danger" @click="sureChoice()" >确定选择该版本</el-button>
        <el-button v-if="isAuth('wms:dailytemp:delete')" type="danger" @click="deleteHandle()" :disabled="dataListSelections.length <= 0">批量删除</el-button>
      </el-form-item>
    </el-form>
    <el-table
      :data="dataList"
      border
      v-loading="dataListLoading"
      @selection-change="selectionChangeHandle"
      style="width: 100%;">
      <el-table-column
        type="selection"
        header-align="center"
        align="center"
        width="50">
      </el-table-column>
      <el-table-column
        prop="id"
        header-align="center"
        align="center"
        label="行ID">
      </el-table-column>
      <el-table-column
        prop="weiboId"
        header-align="center"
        align="center"
        label="微博ID">
      </el-table-column>
      <el-table-column
        prop="weiboContent"
        header-align="center"
        align="center"
        label="微博内容">
      </el-table-column>
      <el-table-column
        prop="weiboPicture"
        header-align="center"
        align="center"
        label="微博图片">
        <template slot-scope="scope">
          <el-button v-if="scope.row.picPathList" :data-img="scope.row.picPathList" type="text" size="small" @click="$imgPreview">查看图片</el-button>
        </template>
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
    <!-- <el-pagination
      @size-change="sizeChangeHandle"
      @current-change="currentChangeHandle"
      :current-page="pageIndex"
      :page-sizes="[10, 20, 50, 100]"
      :page-size="pageSize"
      :total="totalPage"
      layout="total, sizes, prev, pager, next, jumper">
    </el-pagination> -->
    <!-- 弹窗, 新增 / 修改 -->
    <!-- <add-or-update v-if="addOrUpdateVisible" ref="addOrUpdate" @refreshDataList="getDataList"></add-or-update> -->
  </div>
</template>

<script>
  import AddOrUpdate from './dailytemp-add-or-update'
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
        addOrUpdateVisible: false
      }
    },
    components: {
      AddOrUpdate
    },
    activated () {
      this.getDataList()
    },
    watch: {
      dataList: function(val, oldVal){
        this.genContent(val)
      }
    },
    methods: {
      // 获取数据列表
      getDataList () {
        this.dataListLoading = true
        this.$http({
          url: this.$http.adornUrl('/wms/dailytemp/list'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'key': this.dataForm.key
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
      reGenTemp() {
        this.$http({
          url: this.$http.adornUrl('/wms/dailytemp/reGenTemp'),
          method: 'get',
          params: this.$http.adornParams({
            'page': this.pageIndex,
            'limit': this.pageSize,
            'key': this.dataForm.key
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

      // 根据val生成模板内容
      genContent(val) {
        var jsonRes = {}
        val.forEach(item => {
          var weiboContent = item.weiboContent
          var picPathList = item.picPathList
          if(picPathList != null){
              jsonRes[weiboContent] = picPathList.map(pl => {return "<div style='margin: 10px'><img src='" + pl + "'/></div>"}).reduce((x, y) => x + y)
          } else {
            jsonRes[weiboContent] = "<div></div>"
          }
        })
        Lockr.set('dailyJson', jsonRes)
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
            url: this.$http.adornUrl('/wms/dailytemp/delete'),
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
      sureChoice() {
        this.$confirm(`确定已经使用了选择的微博内容作为一个新的发布版本？注意：会逻辑清理已经选择的所有记录`, '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          var idFunArr = Lockr.get('selectFunId')
          var idMotionArr = Lockr.get('selectMotionId')
          var thisTableIdArr = this.dataList.map(el => el.weiboId)
          var collect = thisTableIdArr.concat(idFunArr).concat(idMotionArr)
          var idSet = new Set(collect)
          this.$http({
            url: this.$http.adornUrl('/wms/weibocontent/logicDelete'),
            method: 'post',
            data: this.$http.adornData(idSet, false)
          }).then(({data}) => {
            if (data && data.code === 0) {
              this.$message({
                message: '操作成功',
                type: 'success',
                duration: 1500
              })
              // 清空表格
              this.dataList = []
              // 清空缓存
              Lockr.set('selectId', [])
            } else {
              this.$message.error(data.msg)
            }
          })
        })
      }
    }
  }
</script>
