<template>
      <el-container class="home-container">
        <el-header>
          <div>
            <img src="../assets/logo.png" alt=""/>
            <span>电商后台管理系统</span>
          </div>
          <el-button @click="logout" type="danger">退出登录</el-button>
        </el-header>
        <el-container>
          <el-aside width="200px">
            <el-menu
              :router="true"
              :unique-opened="true"
              background-color="#333744"
              text-color="#fff"
              active-text-color="#409eff"
              :default-active="activePath">
              <el-submenu :index="item.id.toString()" v-for="item in menulist" :key="item.id">
                <template slot="title">
                  <i :class="iconsObj[item.id]"></i>
                  <span>{{item.authName}}</span>
                </template>
                <el-menu-item
                  :index="'/'+subItem.path"
                  v-for="subItem in item.children"
                  :key="subItem.id"
                  @click="saveNavState('/'+subItem.path)">
                  <template slot="title">
                    <i class="el-icon-menu"></i>
                    <span>{{subItem.authName}}</span>
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
    data () {
        return {
          menulist: [],
          iconsObj: {
            125: 'iconfont icon-group_fill',
            103: 'iconfont icon-group_fill',
            101: 'iconfont icon-group_fill',
            102: 'iconfont icon-group_fill',
            145: 'iconfont icon-group_fill'
          },
          activePath: ''
        }
    },
    methods: {
        logout () {
            window.sessionStorage.clear()
            this.$router.push('/login')
        },
        async getMenuList () {
          const { data: res } = await this.$http.get('menus')
          if (res.meta.status !== 200) return this.$message.error('数据请求错误')
          this.menulist = res.data
        },
      saveNavState (activePath) {
          window.sessionStorage.setItem('activePath', activePath)
        this.activePath = activePath
      }
    },
    created () {
      this.getMenuList()
      this.activePath = window.sessionStorage.getItem('activePath')
    }
}

</script>
<style lang="less" scoped>
  .el-header{
    background-color: #373d41;
    display: flex;
    justify-content: space-between;
    align-items: center;
    > div{
      display: flex;
      align-items: center;
      img{
        width: 15%;
        border-radius: 50%;
        background-color: #ffffff;
      }
      span{
        color: #ffffff;
        font-size: 20px;
        margin-left: 15px;
      }
    }
  }
  .el-aside{
    background-color: #333744;
    .el-menu{
      border-right: none;
    }
  }
  .home-container{
    height: 100%;
  }
  .iconfont{
    margin-right: 10px;
  }
  .el-main{
    background-color: #F5F7FA;
  }

</style>
