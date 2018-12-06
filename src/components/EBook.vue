<template>
  <div class="ebook">
      <title-bar :ifTitleAndMenuShow='ifTitleAndMenuShow'></title-bar>
    <div class="read-wrapper">
      <div id="read"></div>
      <div class="mask">
        <div class="left" @click="prevPage"></div>
        <div class="center" @click='toggleTitleAndMenu'></div>
        <div class="right" @click="nextPage"></div>
      </div>
    </div>
    <menu-bar :ifTitleAndMenuShow='ifTitleAndMenuShow' 
    :fontSizeList='fontSizeList'
    ref="menuBar"></menu-bar>
  </div>
</template>

<script>
import TitleBar from '@/components/TitleBar'
import MenuBar from '@/components/MenuBar'
import Epub from "epubjs";
const DOWNLOAD_URL = "/static/test.epub";
global.ePub = Epub;
export default {
    data(){
        return{
            ifTitleAndMenuShow:false,
            fontSizeList: [
                {fontSize:12},
                {fontSize:14},
                {fontSize:16},
                {fontSize:18},
                {fontSize:20},
                {fontSize:22},
                {fontSize:24},
            ]
        }
    },
    components:{
        TitleBar,
        MenuBar
    },
  methods: {
    showEpub() {
      // gen Book
      this.book = new Epub(DOWNLOAD_URL);

      // gen rendition
      this.rendition = this.book.renderTo("read", {
        width: window.innerWidth,
        height: window.innerHeight
      });

      // render ebook
      this.rendition.display();
    },

    prevPage() {
      if (this.rendition) {
        this.rendition.prev();
      }
    },

    nextPage() {
      if (this.rendition) {
        this.rendition.next();
      }
    },

    toggleTitleAndMenu(){
        this.ifTitleAndMenuShow=!this.ifTitleAndMenuShow;

        if(!this.ifTitleAndMenuShow){
            this.$refs.menuBar.hideSetting();
        }
    }
  },
  mounted() {
    this.showEpub();
  }
};
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
    }
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
</style>