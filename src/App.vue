<template>
  <div id="app">
    <div id="nav">
      <a>ホーム管理システム</a>(更新迄{{Math.round(a/60)}}分)
      <span class="date">{{ message }}</span>
    </div>
    <router-view/>
  </div>
</template>
<script>
export default {
  data: function () {
    return {
      time: new Date(),
      a: 3600
    }
  },
  computed: {
    message: function () {
      return this.time.toLocaleString()
    }
  },
  methods: {
    refresh: function () {
      this.time = new Date()
      const self = this
      setTimeout(() => { self.refresh() }, 1000)
    }
  },
  created: function () {
    this.refresh()
    setInterval(() => { this.a-- }, 1000)
  },
  watch: {
    a: function (v) {
      if (v <= 0) {
        document.location = '/'
      }
    }
  }
}
</script>
<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  position: fixed;
  margin-top: -10px;
}
#nav{
  background-color: rgb(3, 89, 217);
  font-size: 3vh;
  font-weight: bold;
  color: white;
}
.date{
  float: right;
  color: white;
  font-size: 2vw;
  font-weight: bold;
}

</style>
