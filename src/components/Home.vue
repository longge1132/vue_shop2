<template>
  <el-container>
    <el-header>
      <img src="../assets/imgs/head.png" alt="#" />
      <span>这是一个简单的网页</span>
      <el-button type="info" @click="logout">退出</el-button>
    </el-header>
    <el-container>
      <el-aside width="225px">
        <el-menu
          :unique-opened="true"
          background-color="#333744"
          text-color="#fff"
          active-text-color="#ffd04b"
          :router="true"
          :default-active="activePath"
        >
          <el-submenu
            :index="'/' + item.path"
            v-for="item in menuList"
            :key="item.id"
          >
            <template slot="title">
              <i class="el-icon-location"></i>
              <span>{{ item.authName }}</span>
            </template>
            <el-menu-item
              :index="'/' + item2.path"
              v-for="item2 in item.children"
              :key="item2.id"
              @click="saveAD(item2.path)"
            >
              <template slot="title">
                <i class="el-icon-setting"></i>
                <span>{{ item2.authName }}</span>
              </template>
            </el-menu-item>
          </el-submenu>
        </el-menu>
      </el-aside>
      <el-main>
        <router-view></router-view>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {
  name: 'myHome',
  data() {
    return {
      menuList: [],
      activePath: '',
    }
  },
  created() {
    this.getMenuList()
    this.activePath = '/' + window.sessionStorage.getItem('activePath')
  },
  methods: {
    logout() {
      window.sessionStorage.clear()
      this.$router.push('/')
    },
    async getMenuList() {
      const { data: res } = await this.$http.get('menus')
      if (res.meta.status !== 200) return this.$message.error(res.meta.msg)
      this.menuList = res.data
      // console.log(res.data)
    },
    saveAD(path) {
      this.activePath = '/' + path
      window.sessionStorage.setItem('activePath', path)
    },
  },
}
</script>

<style lang="less" scoped>
.el-container {
  height: 100%;
  .el-menu {
    border-right: none;
  }
}

.el-header {
  background-color: #333744;
  display: flex;
  justify-content: space-between;
  align-items: center;
  img {
    height: 100%;
    margin-left: -20px;
    width: 225px;
  }
  span {
    font-size: 20px;
    color: #fff;
  }
  .el-button {
    height: 80%;
  }
}

.el-aside {
  background-color: #333744;
  width: 220px;
}

.el-main {
  background-color: #fff;
}
</style>
