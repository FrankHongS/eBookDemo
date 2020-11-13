<template>
  <transition name="slide-right">
    <div class="content">
      <div class="content-wrapper" v-if="bookAvailable">
        <div
          class="content-item"
          v-for="(item, index) of navigation.toc"
          :key="index"
          @click="jumpTo(item.href)"
        >
          <span class="text">{{ item.label }}</span>
        </div>
      </div>
      <div class="empty" v-else>loading...</div>
    </div>
  </transition>
</template>

<script>
export default {
  props: {
    navigation: Object,
    bookAvailable: {
      type: Boolean,
      default: false
    }
  },
  methods: {
    jumpTo(href) {
      this.$emit('jumpTo', href)
    }
  }
}
</script>
<style lang='scss' scoped>
@import "../assets/styles/global";

.content {
  position: absolute;
  top: 0;
  left: 0;
  height: 100%;
  width: px2rem(600);
  background: white;
  z-index: 104;
  display: flex;
  .content-wrapper {
    width: 100%;
    align-items: flex-start;
    .content-item {
      margin: px2rem(25);
      .text {
        font-size: px2rem(25);
        color: #333;
        cursor: pointer;
        &:hover {
          color: blue;
        }
      }
    }
  }
  .empty {
    width: 100%;
    font-size: px2rem(30);
    @include center;
  }
}
</style>
