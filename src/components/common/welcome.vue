<template>
  <div class="welcome" :class="{hide:hide}">
    <img src="../../assets/images/welcome.jpg" alt="">
  </div>
</template>
<script>
  export default {
    data() {
      return {
        hide: false
      };
    },
    mounted() {
      setTimeout(() => {
        this.hide = true;
      }, 1000)
    }
  }
</script>
<style lang="scss" scoped>
  .welcome {
    position: fixed;
    top: 0;
    bottom: 0;
    width: 100%;
    height: 100%;
    z-index: 100;
    background: #000b17;
    animation: welcome 0.5s;
    transition: 0.3s;
    -webkit-transition: 0.3s;
    img {
      width: 100%;
    }
    &.hide {
      opacity: 0;
      visibility: hidden;
    }
  }
</style>

