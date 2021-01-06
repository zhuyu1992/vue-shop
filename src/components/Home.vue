<template>
  <el-container class="home-container">
    <!-- 头部区域 -->
    <el-header>
        <!-- 黑马logo -->
        <!--<img src="../assets/heima.png" alt="">-->
        <!-- 顶部标题 -->
      <el-row :gutter="20">
        <el-col :span="20"><span>电商后台管理系统</span></el-col>
        <el-col :span="4"><span> <el-button type="success" @click="logout"> 退出 </el-button></span></el-col>
      </el-row>
    </el-header>
    <!-- 页面主体区域 -->
    <el-container>
      <!-- 侧边栏 -->
      <el-aside width="200px">
        <!-- 侧边栏菜单 -->
        <el-menu
          background-color="#333744"
          text-color="#fff"
          active-text-color="#ffd04b">
          <!-- 一级菜单 -->
          <el-submenu :index="item.id+''" v-for=" item in menuList" :key="item.id">
            <!-- 一级菜单模板 -->
            <template slot="title">
              <!-- 图标 -->
              <i class="el-icon-location"></i>
              <!-- 文本 -->
              <span>{{item.authName}}</span>
            </template>
            <!-- 二级子菜单 -->
            <el-menu-item index="1-4-1" v-for="subItem in item.children" :key="subItem.id">
              <!-- 二级菜单模板 -->
              <template slot="title">
                <!-- 图标 -->
                <i class="el-icon-location"></i>
                <!-- 文本 -->
                <span>{{subItem.authName}}</span>
              </template>
            </el-menu-item>
          </el-submenu>

        </el-menu>
      </el-aside>
      <!-- 主体结构 -->
      <el-main>Main</el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {
  name: 'Home',
  data () {
    return {
      menuList: []
    }
  },
  created () {
    this.getMenuList()
  },
  methods: {
    logout () {
      window.sessionStorage.clear()
      this.$router.push('/login')
      this.$message.success('退出')
    },
    async getMenuList () {
      const menu = await this.$http.get('menus')
      if (menu.data.meta.status !== 200) {
        return this.$message.error(menu.data.meta.msg)
      }
      this.menuList = menu.data.data
      this.$message.success(menu.data.meta.msg)
    }
  }
}
</script>

<style scoped>
  .home-container{
    height: 100%;
  }
  .el-header, .el-footer {
    background-color: #a7b7c1;
    color: #333;
    text-align: center;
    line-height: 60px;
  }

  .el-aside {
    background-color:#333744;
    color: #333;
    text-align: center;
    line-height: 200px;
  }

  .el-main {
    background-color:#eaedf1;
    color: #333;
    text-align: center;
    line-height: 160px;
  }

  body > .el-container {
    margin-bottom: 40px;
  }

  .el-container:nth-child(5) .el-aside,
  .el-container:nth-child(6) .el-aside {
    line-height: 260px;
  }

  .el-container:nth-child(7) .el-aside {
    line-height: 320px;
  }

</style>
