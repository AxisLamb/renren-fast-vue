<template>
  <div>
    <el-row>
      <el-button-group>
        <el-button type="primary" icon="el-icon-edit" @click='refreshCode'>刷新</el-button>
        <el-button type="warning" icon="el-icon-delete" @click='flushMem'>清空</el-button>
        <el-button type="success" icon="el-icon-check" @click='copyTemp' class="copy-btn" data-clipboard-target="#temp-content">复制模板</el-button>
      </el-button-group>
    </el-row>
    <!-- <button type='button' @click='refreshCode'>刷新</button>
    <button type='button' @click='flushMem'>清空</button>
    <button class="copy-btn" data-clipboard-target="#temp-content" :plain="true" type='button' @click='copyTemp'>复制模板</button> -->
    <div id="temp-content">
      <div v-html="dailyTempData"></div>
      <div v-html="tempCont"></div>
      <div v-html="motionCont"></div>
    </div>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        dailyJson: {},
        funJson: {},
        motionJson: {},
        dailyTempData: "",
        tempCont: "",
        motionCont: ""
      }
    },
    methods: {
      refreshCode(){
        this.dailyTempData = ""
        this.tempCont = ""
        this.motionCont = ""
        var dailyJson = Lockr.get("dailyJson")
        var funJson = Lockr.get("funJson")
        var motionJson = Lockr.get("motionJson")
        var dailyTempData = ""
        var tempCont = ""
        var motionCont = ""
        var _idx = 1
        for(var dailyWeiboCont in dailyJson) {
          var dailyPthList = dailyJson[dailyWeiboCont]
          dailyTempData += _idx.toString() + ". " + dailyWeiboCont + dailyPthList
          _idx ++
        }
        for(var funWeiboCont in funJson) {
          var funPathList = funJson[funWeiboCont]
          tempCont += _idx.toString() + ". " + funWeiboCont + funPathList
          _idx ++
        }
        for(var motionWeiboCont in motionJson) {
          var pathList = motionJson[motionWeiboCont]
          motionCont += _idx.toString() + ". " + motionWeiboCont + pathList
          _idx ++
        }

        // 赋值vue对象
        this.dailyTempData = dailyTempData
        this.tempCont = tempCont
        this.motionCont = motionCont
      },
      flushMem(){
        Lockr.set("dailyTempData","")
        Lockr.set("selectCont","")
        Lockr.set("motionCont","")
        Lockr.set("dailyJson",{})
        Lockr.set("funJson",{})
        Lockr.set("motionJson",{})
        this.dailyTempData = ""
        this.tempCont = ""
        this.motionCont = ""
      },
      copyTemp(){
        let _this = this
        let clipboard = new this.clipboard('.copy-btn')
        clipboard.on('success', function () {
            _this.$message({
              message: '复制成功',
              type: 'success'
            })
            clipboard.destroy();
        })
      }
    }
  }
</script>
