<template>
  <transition name="slide">
    <!-- 因为add-song是playlist的子组件，点击add-song可能冒泡到父组件，所以要使用@click.stop阻止冒泡 -->
    <div class="add-song" v-show="showFlag" @click.stop>
      <div class="header">
        <h1 class="title">添加歌曲到列表</h1>
        <div class="close" @click="hide">
          <i class="icon-close"></i>
        </div>
      </div>
      <div class="search-box-wrapper">
        <search-box
          placeholder="搜索歌曲"
          @query="onQueryChange"
          ref="searchBox"></search-box>
      </div>
      <div class="shortcut" v-show="!query">
        <switches :switches="switches" :current-index="currentIndex" @switch="switchItem"></switches>
        <div class="list-wrapper">
          <scroll
            class="list-scroll"
            v-if="currentIndex===0"
            :data="playHistory"
            ref="songList"
            :refreshDelay="refreshDelay">
            <div class="list-inner">
              <song-list :songs="playHistory" @select="selectSong"></song-list>
            </div>
          </scroll>
          <scroll
            ref="searchList"
            v-if="currentIndex===1"
            class="list-scroll"
            :data="searchHistory">
            <div class="list-inner">
              <search-list
                :searches="searchHistory"
                @select="addQuery"
                @delete="deleteOne"></search-list>
            </div>
          </scroll>
        </div>
      </div>
      <div class="search-result" v-show="query">
        <suggest
          :query="query"
          :showSinger="showSinger"
          @listScroll="blurInput"
          @select="selectSuggest"></suggest>
      </div>

      <top-tip :delay="delay" ref="topTip">
        <div class="tip-title">
          <i class="icon-ok"></i>
          <span class="text">1首歌曲已经添加到播放列表</span>
        </div>
      </top-tip>
    </div>
  </transition>
</template>

<script>
import SearchBox from 'base/search-box/search-box'
import {searchMixin} from 'common/js/mixins.js'
import Suggest from 'components/suggest/suggest'
import Switches from 'base/switches/switches'
import SongList from 'base/song-list/song-list'
import {mapGetters, mapActions} from 'vuex'
import Song from 'common/js/song'
import Scroll from 'base/scroll/scroll'
import SearchList from 'base/search-list/search-list'
import TopTip from '../../base/top-tip/top-tip'

export default {
  name: 'add-song',
  mixins: [searchMixin],
  components: {
    SearchBox,
    Suggest,
    Switches,
    SongList,
    Scroll,
    SearchList,
    TopTip
  },
  data () {
    return {
      showFlag: false,
      showSinger: false,
      switches: [
        {name: '最近播放'},
        {name: '搜索历史'}
      ],
      currentIndex: 0,
      delay: 3000,
      refreshDelay: 100
    }
  },
  computed: {
    ...mapGetters([
      'playHistory'
    ])
  },
  created () {

  },
  methods: {
    ...mapActions([
      'insertSong'
    ]),
    show () {
      this.showFlag = true
      this.$nextTick(() => {
        if (this.currentIndex === 0) {
          this.$refs.songList.refresh()
        } else {
          this.$refs.searchList.refresh()
        }
      })
    },
    hide () {
      this.showFlag = false
    },
    switchItem (index) {
      this.currentIndex = index
    },
    // 选中播放历史中的某一行
    selectSong (item, index) {
      if (index !== 0) {
        this.insertSong(new Song(item))
        this.$refs.topTip.show()
      }
    },
    // 选中搜索列表的某一行
    selectSuggest () {
      // 保存搜索历史
      this.saveSearch()
      // 展示提示框
      this.$refs.topTip.show()
    }
  }
}
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
  @import "~common/stylus/variable"
  @import "~common/stylus/mixin"

  .add-song
    position fixed
    top 0
    bottom 0
    width 100%
    z-index 200
    background $color-background
    &.slide-enter-active, &.slide-leave-active
      transition all 0.3s
    &.slide-enter, &.slide-leave-to
      transform translate3d(100%, 0, 0)
    .header
      position relative
      height 44px
      text-align center
      .title
        line-height 44px
        font-size $font-size-large
        color $color-text
      .close
        position absolute
        top 0
        right 8px
        .icon-close
          display block
          padding 12px
          font-size 20px
          color $color-theme
    .search-box-wrapper
      margin 20px
    .shortcut
      .list-wrapper
        position absolute
        top 165px
        bottom 0
        width 100%
        .list-scroll
          height 100%
          overflow hidden
          .list-inner
            padding 20px 30px
    .search-result
      position fixed
      top 124px
      bottom 0
      width 100%
    .tip-title
      text-align center
      padding 18px 0
      font-size 0
      .icon-ok
        font-size $font-size-medium
        color $color-theme
        margin-right 4px
      .text
        font-size $font-size-medium
        color $color-text
</style>
