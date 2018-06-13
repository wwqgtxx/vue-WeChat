<template>
  <div class="dialogue">
    <header id="wx-header">
      <div class="other">
        <router-link :to="{path:'/wechat/dialogue/dialogue-info',query: { msgInfo: msgInfo}}" tag="span"
                     class="iconfont icon-chat-group"
                     v-show="$route.query.group_num&&$route.query.group_num!=1"></router-link>
        <router-link :to="{path:'/wechat/dialogue/dialogue-detail',query: { msgInfo: msgInfo}}" tag="span"
                     class="iconfont icon-chat-friends" v-show="$route.query.group_num==1"></router-link>
      </div>
      <div class="center">
        <router-link to="/" tag="div" class="iconfont icon-return-arrow">
          <span>微信</span>
        </router-link>
        <span>{{pageName}}</span>
        <span class="parentheses"
              v-show='$route.query.group_num&&$route.query.group_num!=1'>{{$route.query.group_num}}</span>
      </div>
    </header>
    <section class="dialogue-section clearfix" v-on:click="MenuOutsideClick">
      <div class="row clearfix" v-for="(item,index) in msgInfo.msg" :key="index">
        <img :src="item.headerUrl" class="header">
        <p class="text" v-more>{{item.text}}</p>
      </div>
      <span class="msg-more" id="msg-more"><ul>
                    <li>复制</li>
                    <li>转发</li>
                    <li>收藏</li>
                    <li>删除</li>
                </ul></span>
    </section>
    <footer class="dialogue-footer">
      <div class="component-dialogue-bar-person">
        <span class="iconfont icon-dialogue-jianpan" v-show="!currentChatWay" v-on:click="currentChatWay=true"></span>
        <span class="iconfont icon-dialogue-voice" v-show="currentChatWay" v-on:click="currentChatWay=false"></span>
        <div class="chat-way" v-show="!currentChatWay">
          <div class="chat-say" v-press>
            <span class="one">按住 说话</span>
            <span class="two">松开 结束</span>
          </div>
        </div>
        <div class="chat-way" v-show="currentChatWay">
          <input class="chat-txt" type="text" v-on:focus="focusIpt" v-on:blur="blurIpt"/>
        </div>
        <span class="expression iconfont icon-dialogue-smile"></span>
        <span class="more iconfont icon-dialogue-jia"></span>
        <div class="recording" style="display: none;" id="recording">
          <div class="recording-voice" style="display: none;" id="recording-voice">
            <div class="voice-inner">
              <div class="voice-icon"></div>
              <div class="voice-volume">
                <span></span>
                <span></span>
                <span></span>
                <span></span>
                <span></span>
                <span></span>
                <span></span>
                <span></span>
                <span></span>
              </div>
            </div>
            <p>手指上划,取消发送</p>
          </div>
          <div class="recording-cancel" style="display: none;">
            <div class="cancel-inner"></div>
            <p>松开手指,取消发送</p>
          </div>
        </div>
      </div>
    </footer>
  </div>
</template>
<script>
  export default {
    data() {
      return {
        pageName: this.$route.query.name,
        currentChatWay: true, //ture为键盘打字 false为语音输入
        timer: null
        // sayActive: false // false 键盘打字 true 语音输入
      };
    },
    beforeRouteEnter(to, from, next) {
      next(vm => {
        vm.$store.commit("setPageName", vm.$route.query.name)
      });
    },
    computed: {
      msgInfo() {
        for (const i of this.$store.state.msgList.baseMsg) {
          if (i.mid === this.$route.query.mid) {
            return i
          }
        }
      }
    },
    directives: {
      press: {
        inserted(element, binding) {
          let recording = document.querySelector(".recording"),
            recordingVoice = document.querySelector(".recording-voice"),
            recordingCancel = document.querySelector(".recording-cancel"),
            startTx,
            startTy;

          element.addEventListener(
            "touchstart",
            function (e) {
              // 用bind时，vue还没插入到dom,故dom获取为 undefine，用 inserted 代替 bind,也可以开个0秒的定时器
              element.className = "chat-say say-active"
              recording.style.display = recordingVoice.style.display = "block"
              // console.log('start')
              const touches = e.touches[0]
              startTx = touches.clientX
              startTy = touches.clientY
              e.preventDefault()
            },
            false
          );
          element.addEventListener(
            "touchend",
            function (e) {
              /*var touches = e.changedTouches[0];
                          var distanceY = startTy - touches.clientY
                          if (distanceY > 50) {
                              console.log("取消发送信息")
                          }else{
                              console.log("发送信息")
                          }*/

              element.className = "chat-say";
              recordingCancel.style.display = recording.style.display = recordingVoice.style.display =
                "none"
              // console.log('end')
              e.preventDefault()
            },
            false
          );
          element.addEventListener(
            "touchmove",
            function (e) {
              let touches = e.changedTouches[0],
                endTx = touches.clientX,
                endTy = touches.clientY,
                distanceX = startTx - endTx,
                distanceY = startTy - endTy;

              if (distanceY > 50) {
                element.className = "chat-say";
                recordingVoice.style.display = "none";
                recordingCancel.style.display = "block"
              } else {
                element.className = "chat-say say-active"
                recordingVoice.style.display = "block"
                recordingCancel.style.display = "none"
              }
              // 阻断事件冒泡 防止页面被一同向上滑动
              e.preventDefault()
            },
            false
          );
        }
      },
      more: {
        bind(element, binding) {
          let startTx, startTy
          element.addEventListener(
            "touchstart",
            function (e) {
              let msgMore = document.getElementById("msg-more"),
                touches = e.touches[0]
              startTx = touches.clientX
              startTy = touches.clientY

              clearTimeout(this.timer);
              this.timer = setTimeout(() => {
                // 控制菜单的位置
                msgMore.style.left =
                  (startTx - 18 > 180 ? 180 : startTx - 18) + "px"
                msgMore.style.top = element.offsetTop - 33 + "px"
                msgMore.style.display = "block"
                element.style.backgroundColor = "#e5e5e5"
              }, 500)
            },
            false
          );
          element.addEventListener(
            "touchmove",
            function (e) {
              let touches = e.changedTouches[0],
                disY = touches.clientY
              if (Math.abs(disY - startTy) > 10) {
                clearTimeout(this.timer)
              }
            },
            false
          );
          element.addEventListener(
            "touchend",
            function (e) {
              clearTimeout(this.timer)
            },
            false
          );
        }
      }
    },
    methods: {
      // 解决输入法被激活时 底部输入框被遮住问题
      focusIpt() {
        this.timer = setInterval(function () {
          document.body.scrollTop = document.body.scrollHeight
        }, 100);
      },
      blurIpt() {
        clearInterval(this.timer)
      },
      // 点击空白区域，菜单被隐藏
      MenuOutsideClick(e) {
        let container = document.querySelectorAll(".text"),
          msgMore = document.getElementById("msg-more")
        if (e.target.className === "text") {
        } else {
          msgMore.style.display = "none"
          container.forEach(item => (item.style.backgroundColor = "#fff"))
        }
      }
    }
  };
</script>
<style lang="less" scoped>
  // .dialogue header {
  //      position: fixed !important;
  //      top: 0;
  //      width: 100%;
  // }
  .dialogue-section {
    height: 100%;
    background: url("https://sinacloud.net/vue-wechat/images/bg/alarm.jpg");
    background-size: cover;
    padding: 2%;
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
        background: #fff;
        padding: 8px;
        box-sizing: border-box;
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
    .msg-more {
      position: absolute;
      display: none;
      width: 190px;
      padding: 3px;
      left: 0;
      background: #000;
      color: #fff;
      top: 20px;
      left: 20px;
      border-radius: 4px;
      &:after {
        width: 0;
        height: 0;
        position: absolute;
        left: 18px;
        top: 25px;
        content: "";
        border: 6px solid transparent;
        border-top-color: #000;
      }
      ul {
        li {
          float: left;
          width: 46px;
          border-right: 1px solid #fff;
          text-align: center;
          &:last-child {
            border-right-width: 0;
          }
        }
      }
    }
  }

  .dialogue-section-inner {
    width: 100%;
    height: 100%;
    padding: 0 10px;
    overflow: auto;
  }

  .dialogue-footer {
    width: 100%;
    position: fixed;
    bottom: 0;
    left: 0;
    height: 50px;
  }

  /*dialogue-bar*/

  .component-dialogue-bar {
    position: relative;
    height: 100%;
    padding-left: 50px;
    .dialogue-item {
      position: absolute;
      height: 50px;
      bottom: 0;
      left: 0;
      width: 100%;
      transition: 0.25s all ease;
      background-color: #fdfdfd;
      &.transition-dialogue-down {
        bottom: -50px;
      }
      &:before {
        content: "";
        position: absolute;
        width: 200%;
        left: 0;
        top: 0;
        transform: scale(0.5);
        transform-origin: 0 0;
        -webkit-transform: scale(0.5);
        -webkit-transform-origin: 0 0;
        background-color: #b7b7b7;
        height: 1px;
        z-index: 2;
      }
      &:after {
        content: "";
        position: absolute;
        left: 50px;
        top: 0px;
        border-right: 1px solid #b7b7b7;
        height: 200%;
        transform: scale(0.5);
        transform-origin: 0 0;
        z-index: 2;
      }
    }
  }

  .left-slide-type {
    float: left;
    width: 50px;
    height: 100%;
    padding: 5px 0;
    text-align: center;
    font-size: 30px;
    line-height: 40px;
    color: #7d7e83;
    position: relative;
  }

  /*person*/
  .component-dialogue-bar-person {
    overflow: hidden;
    padding: 5px 0;
    height: 100%;
    flex-grow: 1;
    flex-basis: 200px;
    display: flex;
    justify-content: flex-start;
    align-items: flex-start;
    position: relative;
    background-color: #ffffff;
    &::before {
      content: "";
      position: absolute;
      width: 200%;
      left: 0;
      top: 0;
      transform: scale(0.5);
      transform-origin: 0 0;
      -webkit-transform: scale(0.5);
      -webkit-transform-origin: 0 0;
      background-color: #b7b7b7;
      height: 1px;
      z-index: 2;
    }
  }

  .component-dialogue-bar .component-dialogue-bar-person::before {
    display: none;
  }

  .component-dialogue-bar-person .iconfont {
    color: #7d7e83;
    flex-basis: 40px;
    width: 40px;
    padding: 0 3px;
    font-size: 30px;
    flex-grow: 0;
    vertical-align: middle;
    line-height: 40px;
    padding: 0 4px;
  }

  .chat-way {
    vertical-align: middle;
    padding: 4px 0px;
    height: 100%;
    flex-grow: 1;
    flex-basis: 200px;
    .chat-say {
      display: flex;
      justify-content: center;
      align-items: center;
      border-radius: 6px;
      overflow: hidden;
      padding: 0 10px;
      width: 200%;
      height: 200%;
      color: #565656;
      border: 1px solid #7d7e83;
      transform: scale(0.5);
      transform-origin: 0 0;
      font-size: 30px;
    }
    .chat-say_touched {
      background-color: #c6c7ca;
    }
    .two {
      display: none;
    }
    .chat-say_touched {
      .two {
        display: block;
      }
      .one {
        display: none;
      }
    }
    .chat-txt {
      border-radius: 6px;
      overflow: hidden;
      padding: 0 10px;
      width: 200%;
      height: 200%;
      border: 1px solid #7d7e83;
      transform: scale(0.5);
      transform-origin: 0 0;
      font-size: 30px;
    }
  }

  .component-dialogue-bar-public {
    height: 100%;
    display: flex;
    position: relative;
    overflow: hidden;
    li {
      padding: 0 2px;
      overflow: hidden;
      flex-grow: 1;
      flex-shrink: 0;
      line-height: 50px;
      flex-basis: 0;
      display: flex;
      justify-content: center;
      align-items: center;
      position: relative;
      .iconfont {
        font-size: 12px;
      }
      &:not(:last-child)::before {
        content: "";
        position: absolute;
        right: 0;
        top: 0;
        transform-origin: 0 0;
        transform: scale(0.5);
        height: 200%;
        border-right: 1px solid #b7b7b7;
      }
    }
  }

  .recording {
    position: fixed;
    left: 50%;
    top: 45%;
    transform: translate(-50%, -50%);
    width: 140px;
    height: 140px;
    padding: 5px;
    background-color: rgba(0, 0, 0, 0.5);
    color: #ffffff;
    border-radius: 5px;
    font-size: 14px;
    text-align: center;
  }

  .recording-voice {
    .voice-inner {
      display: flex;
      justify-content: center;
      align-items: center;
      height: 110px;
    }
  }

  .voice-inner {
    .voice-icon {
      width: 55px;
      height: 90px;
      background: url(../../assets/images/record-cancel.png) no-repeat center center;
      background-size: contain;
    }
    .voice-volume {
      width: 30px;
      height: 55px;
      span {
        display: block;
        height: 2px;
        margin-top: 4px;
        min-width: 8px;
        float: left;
        clear: both;
        animation-iteration-count: infinite;
        animation-timing-function: linear;
        animation-duration: 2000ms;
        background-color: #e4e4e5;
        &:nth-child(1) {
          width: 24px;
          visibility: hidden;
        }
        &:nth-child(2) {
          width: 22px;
        }
        &:nth-child(3) {
          width: 20px;
        }
        &:nth-child(4) {
          width: 18px;
        }
        &:nth-child(5) {
          width: 16px;
        }
        &:nth-child(6) {
          width: 14px;
        }
        &:nth-child(7) {
          width: 12px;
        }
        &:nth-child(8) {
          width: 10px;
        }
        &:nth-child(9) {
          width: 8px;
        }
      }
    }
  }

  .recording-cancel {
    p {
      border-radius: 3px;
      background-color: #9d383e;
    }
  }

  .cancel-inner {
    width: 110px;
    height: 110px;
    margin: 0 auto;
    background-image: url(../../assets/images/record-cancel.png);
    background-repeat: no-repeat;
    background-position: center center;
    background-size: contain;
  }

  .say-active {
    background: #c6c7ca;
  }
</style>
