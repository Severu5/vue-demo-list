<template>
  <div>
    <h2>首页组件</h2>
    <p>首页内容</p>
    <router-link to="/home/news">新闻</router-link>
    <router-link to="/home/message">消息</router-link>

    <router-view></router-view>
  </div>
</template>

<script>
export default {
  name: "Home",
  data() {
    return {
      path: "/home/news"
    }
  },
  created() {
    console.log('home created')
    console.log(this);
  },
  destroyed(){
    console.log('home destroyed');
  },
  activated() {
    console.log('xxx');
    this.$router.replace(this.path);
  },
  deactivated(){
    console.log('yyy');
  },
  beforeRouteLeave(to, from, next) {
    console.log('beforeLeave');
    this.path = this.$route.path;
    next();
  }
};
</script>
<style scoped>
</style>