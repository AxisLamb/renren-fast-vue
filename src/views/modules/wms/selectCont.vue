<template>
  <div>
    <button type='button' @click='refreshCode'>刷新</button>
    <button type='button' @click='flushMem'>清空</button>
    <div v-html="tempCont"></div>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        tempCont: ""
      }
    },
    methods: {
      refreshCode(){
        this.tempCont = Lockr.get("selectCont")
      },
      flushMem(){
        Lockr.set("selectCont","")
        this.tempCont = ""
      }
    }
  }
</script>
