<template>
  <div>
    <button type='button' @click='refreshCode'>刷新</button>
    <button type='button' @click='flushMem'>清空</button>
    <button class="copy-btn" data-clipboard-target="#temp-content" :plain="true" type='button' @click='copyTemp'>复制模板</button>
    <div id="temp-content">
      <div v-html="tempCont"></div>
      <div v-html="motionCont"></div>
    </div>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        tempCont: "",
        motionCont: ""
      }
    },
    methods: {
      refreshCode(){
        this.tempCont = Lockr.get("selectCont")
        this.motionCont = Lockr.get("motionCont")
      },
      flushMem(){
        Lockr.set("selectCont","")
        Lockr.set("motionCont","")
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
