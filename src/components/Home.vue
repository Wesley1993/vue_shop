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
      <el-aside :width="isCollapse?'64px':'200px'">
        <div class="toggle-button" @click="toggleCollapse">|||</div>
        <el-menu
          background-color="#545c64"
          text-color="#fff"
          active-text-color="#409eff" unique-opened
          :collapse="isCollapse"
          :collapse-transition="false" router
          :default-active="'/'+activePath"
        >
<!--    一级菜单      -->
          <el-submenu :index="'/'+item.path" v-for="item in menuList" :key="item.id">
<!--    一级菜单的模板区域       -->
            <template slot="title">
              <i class="iconfont" :class="iconList[item.id]"></i>
              <span>{{item.authName}}</span>
            </template>
<!--    二级菜单        -->
            <el-menu-item @click="savePath(subItem.path)" :index="'/'+subItem.path" v-for="subItem in item.children" :key="subItem.id">
              <template slot="title">
                <i class="el-icon-menu"></i>
                <span>{{subItem.authName}}</span>
              </template>
            </el-menu-item>
          </el-submenu>
        </el-menu>
      </el-aside>
      <!--      右侧内容主体  -->
      <el-main>
        <router-view></router-view>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {
  name: 'HomePage',
  data () {
    return {
      menuList: [],
      iconList: {
        125: 'icon-user',
        103: 'icon-tijikongjian',
        101: 'icon-shangpin',
        102: 'icon-danju',
        145: 'icon-baobiao'
      },
      isCollapse: false,
      // 被激活的地址
      activePath: ''
    }
  },
  computed: {},
  watch: {},
  created () {
    this.getMenuList()
    this.activePath = window.sessionStorage.getItem('activePath')
  },
  mounted () {
  },
  methods: {
    logout () {
      window.sessionStorage.clear()
      this.$router.push('/login')
    },
    // 获取所有菜单
    async getMenuList() {
      const { data: res } = await this.$http.get('menus')
      if (res.meta.status !== 200) return this.$message.error(res.meta.msg)
      this.menuList = res.data
      console.log(res)
    },
    // 是否折叠菜单
    toggleCollapse() {
      this.isCollapse = !this.isCollapse
    },
    //  保存地址
    savePath(activePath) {
      window.sessionStorage.setItem('activePath', activePath)
      this.activePath = activePath
    }
  }
}
</script>

<style scoped lang="less">
.home-container {
  height: 100%;
}

.el-header {
  background-color: #666;
  display: flex;
  justify-content: space-between;
  padding-left: 10px;
  align-items: center;
  height: 60px;

  .logo {
    display: flex;
    align-items: center;
    color: #fff;
    font-size: 20px;

    span {
      margin-left: 15px;
    }

    img {
      height: 60px;
    }
  }
}

.el-aside {
  background-color: #333744;
  .el-menu {
    border-right: none;
  }
}

.el-main {
  background-color: #eaedf1;
}
.iconfont {
  margin-right: 10px;
}
.toggle-button {
  background-color: #999;
  color: #fff;
  line-height: 24px;
  font-size: 10px;
  text-align: center;
  letter-spacing: .2em;
  cursor: pointer;
}
</style>
