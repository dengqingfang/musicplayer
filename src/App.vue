<template>
  <div id="app">
    <m-header></m-header>
    <tab></tab>
    <!--keep-alive可以将DOM缓存，在页面切换过程中，不会重复发送请求  -->
    <keep-alive>
      <router-view></router-view>
    </keep-alive>
    <!-- 因为在每个页面都能播放 -->
    <player></player>
  </div>
</template>

<script>
import MHeader from 'components/m-header/m-header'
import Tab from 'components/tab/tab'
import Player from './components/player/player'
export default {
  name: 'App',
  components: {
    MHeader,
    Tab,
    Player
  }
}
</script>

<style lang="stylus" rel="stylesheet/stylus">
</style>
