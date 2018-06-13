<template>
  <div id="wx-nav">
    <nav>
      <router-link to="/" tag="dl" exact>
        <dt class="iconfont icon-wechat">
          <i class="new-msg-count" v-show="$store.state.newMsgCount">{{$store.state.newMsgCount}}</i>
        </dt>
        <dd>微信</dd>
      </router-link>
      <router-link to="/contact" tag="dl">
        <dt class="iconfont icon-contact">
          <!--<i class="new-msg-count">2</i>-->
        </dt>
        <dd>通讯录</dd>
      </router-link>
      <router-link to="/explore" tag="dl">
        <dt class="iconfont icon-find">
          <i class="new-msg-dot"></i>
        </dt>
        <dd>发现</dd>
      </router-link>
      <router-link to="/self" tag="dl">
        <dt class="iconfont icon-me">
          <!--<i class="new-msg-dot"></i>-->
        </dt>
        <dd>我</dd>
      </router-link>
    </nav>
  </div>
</template>
<script>
  export default {
    data() {
      return {}
    },
    mounted() {
      for (var i in this.$store.state.msgList.baseMsg) {
        if (this.$store.state.msgList.baseMsg[i].read === false && this.$store.state.msgList.baseMsg[i].quiet === false) {
          this.$store.commit('addNewMsg')
        }
      }
    }
  }
</script>
<style lang="scss">
  #wx-nav {
    nav {
      display: -webkit-box;
      display: -ms-flexbox;
      display: flex;
      width: 100%;
      overflow: hidden;
      height: 50px;
      padding-top: 8px;
      background: #f9f9f9;
      font-size: 12px;
      dl {
        cursor: pointer;
        -moz-user-select: none;
        -ms-user-select: none;
        user-select: none;
        -webkit-user-select: none;
        -webkit-box-flex: 1;
        -ms-flex-positive: 1;
        flex-grow: 1;
        text-align: center;
        line-height: 1;
        &.router-link-active {
          dd,
          dt {
            color: #0bb908;
          }
        }
      }
      dt {
        position: relative;
        width: 28px;
        height: 28px;
        margin: 0 auto;
        font-size: 28px;
        color: #797979;
        margin-bottom: 2px;
      }
      dd {
        color: #929292;
        -webkit-transform-origin: 50% 0;
        transform-origin: 50% 0;
        -webkit-transform: scale(0.9);
        transform: scale(0.9);
      }
    }
  }

</style>
