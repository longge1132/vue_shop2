<template>
  <div class="hello">
    <h1>hello,there is home!</h1>
    <el-button type="info" @click="logout">退出</el-button>
  </div>
</template>

<script>
export default {
  name: 'myHome',
  data() {
    return {
      name: 'jack',
    }
  },
  methods: {
    logout() {
      window.sessionStorage.clear()
      this.$router.push('/')
    },
  },
}
</script>

<style scoped>
.hello {
  background-color: bisque;
}
</style>
