<template>
  <div class="clientNavbar">
    <el-menu class="navbar" :default-active="this.$route.path" router mode="horizontal" @open="handleOpen"
             @close="handleClose" background-color="#545c64" text-color="#fff" active-text-color="#ffd04b">

      <div class="navbar-container">
        <el-menu-item index="/admin/allocateMoney">课题组金额分配</el-menu-item>
        <el-menu-item index="/admin/userManagement">用户（权限）管理</el-menu-item>
        <el-menu-item index="/admin/groupManagement">课题组管理</el-menu-item>
        <el-menu-item index="/admin/fundingManagement">经费管理</el-menu-item>
        <el-menu-item index="/admin/applicationManagement">报销申请管理</el-menu-item>

        <el-menu-item index="/admin/adminMessageNotification">
          <template v-slot:title>
            <i class="el-icon-bell"></i>
            <span>消息中心</span>
          </template>
        </el-menu-item>

        <!--        <el-menu-item index="/">退出登录</el-menu-item>-->
        <el-submenu index="" v-if="isLogin">
          <template v-slot:title>
            <i class="el-icon-user"></i>
            <span>{{ name }} &nbsp  </span>
            <span>{{ role }} &nbsp &nbsp </span>
          </template>
          <el-menu-item index="/" @click="logOut">退出登录</el-menu-item>
        </el-submenu>
        <el-menu-item index="/" v-if="!isLogin">登录</el-menu-item>
      </div>
    </el-menu>
    <router-view></router-view>
  </div>
</template>


<script>
import {ElMessage} from "element-plus";

export default {
  data() {
    return {
      isLogin: false,
      role: "",
      name: "",
      sid: ""
    }
  },
  methods: {
    handleOpen(key, keyPath) {
      console.log(key, keyPath);
    },

    handleClose(key, keyPath) {
      console.log(key, keyPath);
    },

    updateOnline() {
      this.$api.userAPI.updateOnline(this.sid, false).then(resp => {
        if (resp.data.code === 200) {
          ElMessage.info("您已退出登录！")
        }
      })
    },

    logOut() {
      this.isLogin = false
      this.updateOnline();
      sessionStorage.removeItem('state')
    }
  },
  mounted() {
    //用state判断，防止显示出错误
    if (sessionStorage.getItem('state')) {
      this.isLogin = true;
      this.role = JSON.parse(sessionStorage.getItem('state')).user.role;
      this.name = JSON.parse(sessionStorage.getItem('state')).user.name;
      this.sid = JSON.parse(sessionStorage.getItem('state')).user.id;
    }
  },
}
</script>

<style scoped>
.navbar {
  position: relative;
  z-index: 100;
}

.navbar-container {
  display: flex;
  justify-content: flex-end;
}

</style>

