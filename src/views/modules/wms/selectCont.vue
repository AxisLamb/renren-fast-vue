<template>
  <div>
    <button type='button' @click='refreshCode'>刷新</button>
    <button type='button' @click='flushMem'>清空</button>
    <button class="copy-btn" data-clipboard-target="#temp-content" :plain="true" type='button' @click='copyTemp'>复制模板</button>
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
        // this.dailyTempData = Lockr.get("dailyTempData")
        // this.tempCont = Lockr.get("selectCont")
        // this.motionCont = Lockr.get("motionCont")
        this.dailyTempData = ""
        this.tempCont = ""
        this.motionCont = ""
        this.dailyJson = Lockr.get("dailyJson")
        this.funJson = Lockr.get("funJson")
        this.motionJson = Lockr.get("motionJson")
        var _idx = 1
        for(var weiboCont in this.dailyJson) {
          var pathList = this.dailyJson[weiboCont]
          this.dailyTempData += _idx.toString() + ". " + weiboCont + pathList
          _idx ++
        }
        for(var weiboCont in this.funJson) {
          var pathList = this.funJson[weiboCont]
          this.tempCont += _idx.toString() + ". " + weiboCont + pathList
          _idx ++
        }
        for(var weiboCont in this.motionJson) {
          var pathList = this.motionJson[weiboCont]
          this.motionCont += _idx.toString() + ". " + weiboCont + pathList
          _idx ++
        }
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
