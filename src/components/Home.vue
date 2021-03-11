<template>
  <el-container class="home-container ">
<!--    头部区域  -->
    <el-header>
      <div class="logo">
        <img src="../assets/logo.png" alt="">
        <span>电商后台管理系统</span>
      </div>
      <el-button type="info" @click="logout">退出</el-button>
    </el-header>
<!--    页面主体区域  -->
    <el-container>
<!--      侧边栏-->
      <el-aside width="200px">Aside</el-aside>
<!--      右侧内容主体  -->
      <el-main>Main</el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {
  name: 'HomePage',
  data () {
    return {}
  },
  computed: {},
  watch: {},
  created () {
  },
  mounted () {
  },
  methods: {
    logout () {
      window.sessionStorage.clear()
      this.$router.push('/login')
    }
  }
}
</script>

<style scoped lang="less">
.home-container {
  height: 100%;
}
.el-header {
  background-color:#666;
  display: flex;
  justify-content: space-between;
  padding-left: 10px;
  align-items: center;
  height: 60px;
  .logo {
    display: flex;
    align-items: center;
    span{
      color: #fff;
      font-size: 16px;
      margin-left: 15px;
    }
    img {
      height: 60px;
    }
  }
}
.el-aside {
  background-color:#333744;
}
.el-main {
  background-color: #eaedf1;
}
</style>
