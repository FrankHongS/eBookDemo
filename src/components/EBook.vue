<template>
  <div class="ebook">
    <title-bar :ifTitleAndMenuShow="ifTitleAndMenuShow"></title-bar>
    <div class="read-wrapper">
      <div id="read"></div>
      <div class="mask">
        <div class="left" @click="prevPage"></div>
        <div class="center" @click="toggleTitleAndMenu"></div>
        <div class="right" @click="nextPage"></div>
      </div>
    </div>
    <menu-bar
      :ifTitleAndMenuShow="ifTitleAndMenuShow"
      :fontSizeList="fontSizeList"
      :currentFontSize="currentFontSize"
      @setFontSize="setFontSize"
      :themeList="themeList"
      :currentTheme="currentTheme"
      @setTheme="setTheme"
      :bookAvailable="bookAvailable"
      @onProgressChange="onProgressChange"
      :navigation="navigation"
      @jumpTo="jumpTo"
      ref="menuBar"
    ></menu-bar>
  </div>
</template>

<script>
import TitleBar from '@/components/TitleBar'
import MenuBar from '@/components/MenuBar'
import Epub from 'epubjs'
const DOWNLOAD_URL = '/static/test.epub'
global.ePub = Epub
export default {
  data() {
    return {
      ifTitleAndMenuShow: false,
      fontSizeList: [
        { fontSize: 12 },
        { fontSize: 14 },
        { fontSize: 16 },
        { fontSize: 18 },
        { fontSize: 20 },
        { fontSize: 22 },
        { fontSize: 24 }
      ],
      currentFontSize: 16,
      themeList: [
        {
          name: 'default',
          style: {
            body: {
              color: '#000',
              background: '#fff'
            }
          }
        },
        {
          name: 'eye',
          style: {
            body: {
              color: '#000',
              background: '#c7edcc'
            }
          }
        },
        {
          name: 'night',
          style: {
            body: {
              color: '#fff',
              background: '#000'
            }
          }
        },
        {
          name: 'gold',
          style: {
            body: {
              color: '#000',
              background: 'rgb(241,236,226)'
            }
          }
        }
      ],
      currentTheme: 0,
      bookAvailable: false
    }
  },
  components: {
    TitleBar,
    MenuBar
  },
  methods: {
    showEpub() {
      // gen Book
      this.book = new Epub(DOWNLOAD_URL)

      // gen rendition
      this.rendition = this.book.renderTo('read', {
        width: window.innerWidth,
        height: window.innerHeight
      })

      // render ebook
      this.rendition.display()

      // gen theme
      this.themes = this.rendition.themes
      // set default font size
      this.setFontSize(this.currentFontSize)
      // register theme
      this.registerTheme()
      // set default theme
      this.setTheme(this.currentTheme)

      // gen locations
      this.book.ready
        .then(() => {
          // gen navigation
          this.navigation = this.book.navigation
          console.log(this.navigation)
          return this.book.locations.generate()
        })
        .then(result => {
          this.locations = this.book.locations
          this.bookAvailable = true
        })
    },

    prevPage() {
      if (this.rendition) {
        this.rendition.prev()
      }
    },

    nextPage() {
      if (this.rendition) {
        this.rendition.next()
      }
    },

    hideAll() {
      this.toggleTitleAndMenu()
      this.$refs.menuBar.hideBookIndex()
    },

    toggleTitleAndMenu() {
      this.ifTitleAndMenuShow = !this.ifTitleAndMenuShow

      if (!this.ifTitleAndMenuShow) {
        this.$refs.menuBar.hideSetting()
      }
    },
    // set font size
    setFontSize(fontSize) {
      if (this.themes) {
        this.themes.fontSize(fontSize + 'px')
        this.currentFontSize = fontSize
      }
    },
    registerTheme() {
      this.themeList.forEach(theme => {
        this.themes.register(theme.name, theme.style)
      })
    },
    // set theme
    setTheme(index) {
      if (this.currentTheme !== index) {
        this.themes.select(this.themeList[index].name)
        this.currentTheme = index
      }
    },

    onProgressChange(progress) {
      const percent = progress / 100
      const location =
        percent > 0 ? this.locations.cfiFromPercentage(percent) : 0

      this.rendition.display(location)
    },

    jumpTo(href) {
      this.rendition.display(href)
      this.toggleTitleAndMenu()
    }
  },
  mounted() {
    this.showEpub()
  }
}
</script>
<style lang='scss' scoped>
@import "../assets/styles/global";

.ebook {
  position: relative;
  overflow: hidden;

  .read-wrapper {
    .mask {
      position: absolute;
      top: 0;
      left: 0;
      z-index: 100;
      display: flex;
      width: 100%;
      height: 100%;
      .left {
        flex: 0 0 px2rem(100);
      }
      .center {
        flex: 1;
      }
      .right {
        flex: 0 0 px2rem(100);
      }
    }
  }
}
</style>
