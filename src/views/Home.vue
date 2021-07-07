<template>
  <div class="home">
    <h1>Jemma 在前台</h1>
    <div id="nav">
      <router-link to="/">首頁</router-link> |
      <router-link to="/products">前台產品頁面</router-link>
    </div>
    <!-- 顯示畫面 -->
    <router-view></router-view>
  </div>
</template>

<script>
export default {
  name: 'Home',
  data() {
    return {
    };
  },
};
</script>
