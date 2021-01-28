<template>
  <nav class="site-navbar" :class="'site-navbar--' + navbarLayoutType">
        <div class="site-navbar__header">
          <h1 class="site-navbar__brand" @click="$router.push({ name: 'home' })">
            <a class="site-navbar__brand-lg" href="javascript:;">人人快速开发平台11</a>
            <a class="site-navbar__brand-mini" href="javascript:;">人人</a>
          </h1>
        </div>
    <div class="site-navbar__body clearfix">
      <el-menu
        class="site-navbar__menu"
        mode="horizontal">
        <el-menu-item class="site-navbar__switch" index="0" @click="sidebarFold = !sidebarFold"></el-menu-item>
<!--        <el-menu-item class="site-navbar__switch" index="0">-->
<!--          <img @click="goMain" style="width: 150px;height: 150px;position: fixed; top: -20px;left: 10px" :src="logoUrl">-->
<!--        </el-menu-item>-->
      </el-menu>
      <el-menu
        class="site-navbar__menu site-navbar__menu--right"
        mode="horizontal">
        <!--        <el-menu-item index="1" @click="$router.push({ name: 'theme' })">-->
        <!--          <template slot="title">-->
        <!--            <el-badge value="new">-->
        <!--              <icon-svg name="shezhi" class="el-icon-setting"></icon-svg>-->
        <!--            </el-badge>-->
        <!--          </template>-->
        <!--        </el-menu-item>-->
        <!--        <el-menu-item index="2">-->
        <!--          <el-badge value="hot">-->
        <!--            <a href="https://www.renren.io/" target="_blank">社区精选</a>-->
        <!--          </el-badge>-->
        <!--        </el-menu-item>-->
        <el-menu-item index="1">
          <a href="https://www.renren.io/" target="_blank">社区精选</a>
        </el-menu-item>
        <el-menu-item index="2">
          <a href="https://www.renren.io/" target="_blank">排行榜</a>
        </el-menu-item>
        <el-menu-item index="4">
          <a href="https://www.renren.io/" target="_blank">历史记录</a>
        </el-menu-item>
        <el-submenu index="5">
          <template slot="title">导航列表</template>
<!--                    <el-menu-item index="2-1"><a href="https://github.com/renrenio/renren-fast-vue" target="_blank">前端</a></el-menu-item>-->
<!--                    <el-menu-item index="2-2"><a href="https://gitee.com/renrenio/renren-fast" target="_blank">后台</a></el-menu-item>-->
<!--                    <el-menu-item index="2-3"><a href="https://gitee.com/renrenio/renren-generator" target="_blank">代码生成器</a></el-menu-item>-->
          <el-menu-item index="home" @click="$router.push({ name: 'home' })">
            <icon-svg name="shouye" class="site-sidebar__menu-icon"></icon-svg>
            <span slot="title">首页</span>
          </el-menu-item>
          <el-submenu index="demo">
            <template slot="title">
              <icon-svg name="shoucang" class="site-sidebar__menu-icon"></icon-svg>
              <span>demo</span>
            </template>
            <el-menu-item index="demo-echarts" @click="$router.push({ name: 'demo-echarts' })">
              <icon-svg name="tubiao" class="site-sidebar__menu-icon"></icon-svg>
              <span slot="title">echarts</span>
            </el-menu-item>
            <el-menu-item index="demo-ueditor" @click="$router.push({ name: 'demo-ueditor' })">
              <icon-svg name="editor" class="site-sidebar__menu-icon"></icon-svg>
              <span slot="title">ueditor</span>
            </el-menu-item>
          </el-submenu>
          <sub-menu
            v-for="menu in menuList"
            :key="menu.menuId"
            :menu="menu"
            :dynamicMenuRoutes="dynamicMenuRoutes">
          </sub-menu>
        </el-submenu>
        <el-menu-item class="site-navbar__avatar" index="3">
          <el-dropdown :show-timeout="0" placement="bottom">
            <span class="el-dropdown-link">
              <img src="~@/assets/img/avatar.png" :alt="userName"><span style="color: #fff">{{ userName }}</span>
            </span>
            <el-dropdown-menu slot="dropdown">
              <el-dropdown-item @click.native="updatePasswordHandle()">修改密码</el-dropdown-item>
              <el-dropdown-item @click.native="logoutHandle()">退出</el-dropdown-item>
            </el-dropdown-menu>
          </el-dropdown>
        </el-menu-item>
      </el-menu>
    </div>
    <!-- 弹窗, 修改密码 -->
    <update-password v-if="updatePassowrdVisible" ref="updatePassowrd"></update-password>
  </nav>
</template>

<script>
import UpdatePassword from './main-navbar-update-password'
import {clearLoginInfo} from '@/utils'
import SubMenu from './main-sidebar-sub-menu'
import {isURL} from '@/utils/validate'

export default {
  data() {
    return {
      updatePassowrdVisible: false,
      dynamicMenuRoutes: [],
      logoUrl: './src/assets/img/logo.png'
    }
  },
  components: {
    UpdatePassword,
    SubMenu
  },
  created() {
    this.menuList = JSON.parse(sessionStorage.getItem('menuList') || '[]')
    this.dynamicMenuRoutes = JSON.parse(sessionStorage.getItem('dynamicMenuRoutes') || '[]')
    this.routeHandle(this.$route)
  },
  computed: {
    navbarLayoutType: {
      get() {
        return this.$store.state.common.navbarLayoutType
      }
    },
    sidebarLayoutSkin: {
      get() {
        return this.$store.state.common.sidebarLayoutSkin
      }
    },
    menuList: {
      get() {
        return this.$store.state.common.menuList
      },
      set(val) {
        this.$store.commit('common/updateMenuList', val)
      }
    },
    menuActiveName: {
      get() {
        return this.$store.state.common.menuActiveName
      },
      set(val) {
        this.$store.commit('common/updateMenuActiveName', val)
      }
    },
    mainTabs: {
      get() {
        return this.$store.state.common.mainTabs
      },
      set(val) {
        this.$store.commit('common/updateMainTabs', val)
      }
    },
    mainTabsActiveName: {
      get() {
        return this.$store.state.common.mainTabsActiveName
      },
      set(val) {
        this.$store.commit('common/updateMainTabsActiveName', val)
      }
    },
    sidebarFold: {
      get() {
        return this.$store.state.common.sidebarFold
      },
      set(val) {
        this.$store.commit('common/updateSidebarFold', val)
      }
    },
    userName: {
      get() {
        return this.$store.state.user.name
      }
    }
  },
  watch: {
    $route: 'routeHandle'
  },
  methods: {
    goMain() {
      this.$router.push({path: '/sys-store-main'})
    },
    routeHandle(route) {
      if (route.meta.isTab) {
        // tab选中, 不存在先添加
        var tab = this.mainTabs.filter(item => item.name === route.name)[0]
        if (!tab) {
          if (route.meta.isDynamic) {
            route = this.dynamicMenuRoutes.filter(item => item.name === route.name)[0]
            if (!route) {
              return console.error('未能找到可用标签页!')
            }
          }
          tab = {
            menuId: route.meta.menuId || route.name,
            name: route.name,
            title: route.meta.title,
            type: isURL(route.meta.iframeUrl) ? 'iframe' : 'module',
            iframeUrl: route.meta.iframeUrl || '',
            params: route.params,
            query: route.query
          }
          this.mainTabs = this.mainTabs.concat(tab)
        }
        this.menuActiveName = tab.menuId + ''
        this.mainTabsActiveName = tab.name
      }
    },
    // 修改密码
    updatePasswordHandle() {
      this.updatePassowrdVisible = true
      this.$nextTick(() => {
        this.$refs.updatePassowrd.init()
      })
    },
    // 退出
    logoutHandle() {
      this.$confirm(`确定进行[退出]操作?`, '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        this.$http({
          url: this.$http.adornUrl('/sys/logout'),
          method: 'post',
          data: this.$http.adornData()
        }).then(({data}) => {
          if (data && data.code === 0) {
            clearLoginInfo()
            this.$router.push({name: 'login'})
          }
        })
      }).catch(() => {
      })
    }
  }
}
</script>
