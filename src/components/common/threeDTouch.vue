<template>
  <!--模拟 iOS 的 3DTouch 效果 效果不满意 未使用-->
  <div class="threeD">
    <div class="main">
      <header>收购万达讨论群</header>
      <!--<section class="dialogue-section">
          <div class="row clearfix" v-for="item in msgInfo.msg">
              <img :src="item.headerUrl" class="header">
              <p class="text" v-more>{{item.text}}</p>
          </div>
      </section>-->
      <section class="dialogue-section clearfix">
        <div class="row clearfix"><img src="https://sinacloud.net/vue-wechat/images/headers/sunquan.jpg" class="header">
          <p class="text">点击消息，唤醒消息操作菜单</p>
        </div>
        <div class="row clearfix"><img src="https://sinacloud.net/vue-wechat/images/headers/sunquan.jpg" class="header">
          <p class="text">点击空白处，操作菜单消失</p>
        </div>
        <div class="row clearfix"><img src="https://sinacloud.net/vue-wechat/images/headers/sunquan.jpg" class="header">
          <p class="text">容我三思</p>
        </div>
      </section>
    </div>
  </div>
</template>
<script>
  export default {
    props: ["msgInfo"]
  }
</script>
<style lang="scss">
  .threeD {
    position: absolute;
    z-index: 5;
    display: none;
    top: 0;
    width: 100%;
    bottom: 0;
    .main {
      width: 80%;
      border-radius: 5px;
      margin: 0 auto;
      overflow: hidden;
      margin-top: 30%;
      position: relative;
      z-index: 4;
      header {
        height: 40px;
        width: 100%;
        text-align: center;
        line-height: 40px;
        color: #fff;
        background: #1b1b1b;
      }
    }
    .dialogue-section {
      height: calc(100% - 50px);
      background: url("https://sinacloud.net/vue-wechat/images/bg/mao.jpg") center center;
      padding: 2%;
      min-height: 350px;
      .row {
        width: 80%;
        margin-top: 30px;
        margin-bottom: -10px;
        .header {
          width: 35px;
          float: left;
          display: block;
        }
        .text {
          float: left;
          /* line-height: 40px; */
          background: #fff;
          /* display: block; */
          padding: 8px;
          box-sizing: border-box;
          /* height: 40px; */
          margin-left: 10px;
          position: relative;
          border-radius: 4px;
          max-width: 80%;
          font-size: 14px;
          &:before {
            width: 0;
            height: 0;
            position: absolute;
            left: -12px;
            top: 11px;
            content: "";
            border: 6px solid transparent;
            border-right-color: #fff;
          }
        }
      }
    }
  }

</style>
