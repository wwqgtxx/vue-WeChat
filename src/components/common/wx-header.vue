<template>
  <!--复用性高，数据交互比较频繁-->
  <div id="wx-header">
    <!--右上角图标-->
    <div class="other">
      <!--只在“微信”页显示 更多图标-->
      <span class="iconfont icon-tips-jia" v-show="$route.path==='/'"
            v-on:click="$store.commit('toggleTipsStatus')"></span>
      <!--只在“通讯录”页显示 显示添加好友图标-->
      <router-link tag="span" to="/contact/add-friend" class="iconfont icon-tips-add-friend"
                   v-show="$route.path==='/contact'"></router-link>
      <!--只在“添加朋友”页显示 -->
      <span v-show="$route.path==='/contact/new-friends'">添加朋友</span>
      <!--下面这个好像有些多余 sad -->
      <span class="iconfont icon-chat-friends" v-show="$route.path==='/wechat/dialogue'"></span>
      <!-- 更多图标的菜单 附带过渡效果-->
      <ul class="tips-menu" :class="[$store.state.tipsStatus?'tips-open':'tips-close']"
          v-on:click="$store.commit('toggleTipsStatus', -1)">
        <li>
          <span class="iconfont icon-tips-xiaoxi"></span>
          <div>发起群聊</div>
        </li>
        <router-link tag="li" to="/wehchat/add-friend">
          <span class="iconfont icon-tips-add-friend"></span>
          <div>添加朋友</div>
        </router-link>
        <li><span class="iconfont icon-tips-saoyisao"></span>
          <div>扫一扫</div>
        </li>
        <li><span class="iconfont icon-tips-fukuan"></span>
          <div>收付款</div>
        </li>
      </ul>
      <!--<div class="tips-masker" v-show="tips_isOpen"></div>-->
    </div>
    <div class="center">
      <!-- <transition name="fade">
          <div class="iconfont icon-return-arrow" style="left: 10px;position: absolute;font-size: 16px;" v-on:click="goBack" v-show="$route.path.split('/').length>2"><span>{{$store.state.backPageName}}</span></div>
      </transition>-->
      <!--显示当前页的名字-->
      <span>{{$store.state.currentPageName}}</span>
      <!--微信群 显示群名以及成员人数 好像和 dialogue 组件 写重了 sad -->
      <span class="parentheses"
            v-show='$route.query.group_num&&$route.query.group_num!=1'>{{$route.query.group_num}}</span>
    </div>
  </div>
</template>
<script>
  export default {
    data() {
      return {
        // 暂且用不到
        chatCount: true
      }
    },
    methods: {
      // 暂且用不到 先留着
      goBack() {
        this.$router.go(-1)
        //保证返回操作后正确显示页面名称
        // this.$store.commit("setPageName", this.$store.state.backPageName)
      }
    }
  }
</script>
<style lang="scss">
  #wx-header {
    position: relative;
    z-index: 99;
    // overflow: hidden;
    height: 45px;
    // font-size: 15px;
    padding: 0 15px 0 10px;
    line-height: 45px;
    background: #1b1b1b;
    opacity: 1;
    color: #fff;
    user-select: none;
    -webkit-user-select: none;
    transition: all 0.3s linear;
    .center {
      margin: 0 auto;
      text-align: center;
      font-size: 17px;
      span {
        font-family: Helvetica Neue, Helvetica, PingFang SC, Hiragino Sans GB, Microsoft YaHei, Noto Sans CJK SC, WenQuanYi Micro Hei, Arial, sans-serif
      }
      .icon-return-arrow {
        left: 10px;
        position: absolute;
        font-size: 16px;
      }
    }
    .other {
      position: absolute;
      cursor: pointer;
      right: 10px;
      & > span {
        font-size: 16px;
        display: inline-block;
      }
      & > .iconfont {
        font-size: 22px;
        width: 40px;
        text-align: center;
      }
      .tips-masker {
        position: fixed;
        left: 0;
        top: 0;
        width: 100%;
        z-index: 1;
        top: 45px;
        bottom: 50px;
      }
      .tips-menu {
        position: absolute;
        z-index: 2;
        width: 133px;
        font-size: 16px;
        right: 0;
        top: 54px;
        text-align: left;
        border-radius: 2px;
        background-color: #49484b;
        padding: 0 15px;
        -webkit-transform-origin: 90% 0%;
        transform-origin: 90% 0%;
      }
    }
    .tips-open {
      -webkit-transition: initial;
      transition: initial;
      opacity: 1;
    }
    .tips-close {
      opacity: 0;
      -webkit-transition: .2s opacity ease, .6s transform ease;
      transition: .2s opacity ease, .6s transform ease;
      -webkit-transform: scale(0);
      transform: scale(0);
    }
    .other .tips-menu {
      li {
        position: relative;
        height: 40px;
        line-height: 40px;
        &:not(:last-child)::after {
          content: "";
          width: 200%;
          position: absolute;
          bottom: 0;
          left: 0;
          height: 1px;
          background-color: #5b5b5d;
          -webkit-transform: scale(0.5);
          transform: scale(0.5);
          -webkit-transform-origin: 0 100%;
          transform-origin: 0 100%;
        }
      }
      &::before {
        width: 0;
        height: 0;
        position: absolute;
        top: -7px;
        right: 15px;
        content: "";
        border-width: 0 6px 8px;
        border-color: rgba(0, 0, 0, 0) rgba(0, 0, 0, 0) #49484b rgba(0, 0, 0, 0);
        border-style: solid;
      }
      .iconfont {
        float: left;
        font-size: 16px;
        margin-right: 15px;
      }
    }
  }

</style>
