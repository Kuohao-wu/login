<template>
  <div>
    <el-menu theme="dark" :default-active="activeIndex" class="el-menu-demo" mode="horizontal">
      <el-menu-item index="1"><router-link to="/">主页</router-link></el-menu-item>
      <el-menu-item index="2" :style="{float: 'right'}">
        <router-link v-show="!user.name" to="/login">登录</router-link>
        <el-dropdown @command="loginOut">
          <span :style="{color:'#FFF'}" v-show="user.name">
          {{user.name}}<i class="el-icon-caret-bottom el-icon--right"></i>
          </span>
          <el-dropdown-menu slot="dropdown">
            <el-dropdown-item command>登出</el-dropdown-item>
          </el-dropdown-menu>
        </el-dropdown>
      </el-menu-item>
    </el-menu>
    <el-card class="box-card">
      <p>Hello {{user.name}}</p>
    </el-card>
  </div>
</template>

<script>
  import {mapActions} from 'vuex'
  export default {
    data() {
      return {
        activeIndex:'1',
        user:{
          name:''
        }
      }
    },
    beforeCreate(){
      // 当主页刷新时，如果服务端设置的token
      // 的时效到了的话，便会提示未登录
      this.$http.get('/api/token')
        .then(res => {
          console.dir(res.data)
          if (res.data.error) {
            this.userLoginOut();
            this.$message.error(res.data.error);
            this.user.name = null;
            return false;
          }else{
            let username = localStorage.getItem('username');
            if (username) {
              this.user.name = username;
            }
          }
        })
        .catch(err => {
            this.$message.error(`${err.message}`)
        })
      
    },
    methods: {
      ...mapActions(['userLoginOut']),
      // 登出loginOut
      loginOut(){
        this.userLoginOut();
        this.user.name = null;
        if (!this.$store.state.token) {
            this.$router.push('/login')
            this.$message.success('登出成功');
        } else {
            this.$message.success('登出失败');
        }
        
      }
    }
  }
</script>
