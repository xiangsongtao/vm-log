<template>
    <section class="logBox" :class="{'active':isActive}">
        <article ref="listBox" class="logBox__article">
            <section class="recordList">
                <!--console each start-->
                <section v-show="selectType==='console'">
                    <section class="grid">
                        <h5 class="titleColor">记录</h5>
                        <div class="recordItem" v-for="item in recordList" :class="[setTypeClass(item.type)]">
                            <div class="recordItem__count">
                                <span v-if="parseInt(item.count)>99">99+</span>
                                <span v-else>{{item.count}}</span>
                            </div>
                            <div class="recordItem__info">
                                <div class="recordItem__info--names">
                                    <span class="type">{{item.type}}</span>
                                    <i v-if="!!item.name" class="name">{{item.name}}</i>
                                    <i v-if="!!item.time" class="time">- {{new Date(item.time).Format('yyyy/MM/dd hh:mm:ss')}}</i>
                                </div>
                                <div class="recordItem__info--position" v-if="!!item.script">
                                    <span class="name">Position:</span>
                                    <i v-if="!!item.script" class="script">{{item.script}}</i>
                                    <i v-if="!!item.line" class="line">-{{item.line}}</i>
                                </div>
                                <div class="recordItem__info--message" v-if="!!item.message">
                                    <span class="name">Message: </span>
                                    <span>{{item.message}}</span>
                                </div>
                                <div class="recordItem__info--stack" v-if="!!item.stack">
                                    <span class="name">Stack: </span>
                                    <span>{{item.stack}}</span>
                                </div>
                            </div>
                        </div>
                        <h5 class="titleColor">操作</h5>
                        <section class="row">
                            <section class="col">
                                <button class="button" block @click="clear">clear</button>
                            </section>
                        </section>
                    </section>
                </section>
                <!--console each end-->
    
                <!-- vimo info start-->
                <div class="recordItem" v-show="selectType==='vimo'" v-if="platform">
                    <section class="grid">
                        <!-- platform -->
                        <h5 class="titleColor">当前平台信息</h5>
                        <section class="row">
                            <section class="col" col-4>
                                <strong>isReady:</strong>
                            </section>
                            <section class="col" col-8 v-if="isPlatformReady">
                                <strong class="isReady">TRUE</strong>
                            </section>
                            <section class="col" col-8 v-else>
                                <strong class="isNotReady">FALSE</strong>
                            </section>
                        </section>
    
                        <!--AlipayJSBridge/ap-->
    
                        <section class="row">
                            <section class="col" col-4>
                                <strong>设备平台:</strong>
                            </section>
                            <section class="col" col-8>{{platform.navigatorPlatform()}}</section>
                        </section>
                        <section class="row">
                            <section class="col" col-4>
                                <strong>文字方向:</strong>
                            </section>
                            <section class="col" col-8>{{platform.dir()}}</section>
                        </section>
                        <section class="row">
                            <section class="col" col-4>
                                <strong>设备语言:</strong>
                            </section>
                            <section class="col" col-8>{{platform.lang()}}</section>
                        </section>
                        <section class="row">
                            <section class="col" col-4>
                                <strong>网路类型:</strong>
                            </section>
                            <section class="col" col-8>{{platform.netType() || '未检测到'}}</section>
                        </section>
                        <section class="row">
                            <section class="col" col-4>
                                <strong>屏幕方向:</strong>
                            </section>
                            <section class="col" col-8>
                                {{platform.isPortrait() === null ? '未检测到' : platform.isPortrait() === true ? '竖屏' : '横屏'}}
                            </section>
                        </section>
    
                        <!--屏幕尺寸-->
                        <section class="row" v-if="platform._isPortrait !== null">
                            <section class="col" col-4>
                                <strong>屏幕尺寸:</strong>
                            </section>
                            <section class="col" col-8>
                                <section class="row">高度: {{platform.height()}}px</section>
                                <section class="row">宽度: {{platform.width()}}px</section>
                            </section>
                        </section>
    
                        <section class="row">
                            <section class="col" col-4>
                                <strong>平台等级:</strong>
                            </section>
                            <section class="col" col-8>
                                <strong>{{platform.platforms().join(' -> ')}}</strong>
                            </section>
                        </section>
    
                        <section class="row">
                            <section class="col" col-4>
                                <strong>地址栏参数:</strong>
                            </section>
    
                            <section class="col" col-8 v-if="JSON.stringify(platform._qp.data) !== '{}'">
                                <div class="detailBox" v-for="(value,key) in platform._qp.data">
                                    <section class="row">
                                        <span class="detailBox__title">{{key}}: </span>
                                        <span class="detailBox__value">{{value}}</span>
                                    </section>
                                </div>
                            </section>
                            <section class="col" col-8 v-else>
                                <section class="row">无参数</section>
                            </section>
                        </section>
    
                        <section class="row">
                            <section class="col" col-4>
                                <strong>UserAgent:</strong>
                            </section>
                            <section class="col" col-8>{{platform.userAgent()}}</section>
                        </section>
                        <section class="row">
                            <section class="col" col-4>
                                <strong>平台版本:</strong>
                            </section>
                            <section class="col" col-8>
                                <div class="detailBox" v-for="(value,key) in platform.versions()" v-if="!!value">
                                    <section class="row">
                                        <span class="detailBox__title">{{key}}: </span>
                                        <span class="detailBox__value">{{value.str}}</span>
                                    </section>
                                </div>
                            </section>
                        </section>
                        <section class="row">
                            <section class="col" col-4>
                                <strong>CSS属性:</strong>
                            </section>
                            <section class="col" col-8>
                                <div class="detailBox" v-for="(value,key) in platform.css">
                                    <section class="row">
                                        <span class="detailBox__title">
                                            <strong>{{key}}:</strong>
                                        </span>
                                    </section>
                                    <section class="row">
                                        <span class="detailBox__value">{{value}}</span>
                                    </section>
                                </div>
                            </section>
                        </section>
    
                        <section class="row">
                            <section class="col" col-4>
                                <strong>平台方法:</strong>
                            </section>
                            <section class="col" col-8>
                                <div class="detailBox" v-for="key in thisKeysOfRegisterMethod" v-if="thisKeysOfRegisterMethod.length > 0">
                                    <section class="row">
                                        <span class="detailBox__title">
                                            <strong>{{key}}</strong>
                                        </span>
                                    </section>
                                </div>
                                <div class="detailBox" v-if="thisKeysOfRegisterMethod.length === 0">无</div>
                            </section>
                        </section>
    
                        <h5 class="titleColor">当前用户配置</h5>
                        <section class="row" v-for="(value,key) in settingConfig" :key="key">
                            <section class="col" col-4>
                                <strong>{{key}}:</strong>
                            </section>
                            <section class="col" col-8>
                                {{value}}
                            </section>
                        </section>
    
                        <h5 class="titleColor">已缓存配置</h5>
                        <section class="row" v-for="(value,key) in cachedConfig" :key="key">
                            <section class="col" col-4>
                                <strong>{{key}}:</strong>
                            </section>
                            <section class="col" col-8>
                                {{value}}
                            </section>
                        </section>
                        <h5 class="titleColor">操作</h5>
                        <section class="row">
                            <section class="col">
                                <button class="button" block @click="refreshVimoData">Refresh</button>
                            </section>
                        </section>
                    </section>
                </div>
                <!-- platform info end-->
    
                <!-- webStorage info start-->
    
                <div class="recordItem" v-show="selectType==='storage'">
                    <section class="grid">
                        <!-- platform -->
                        <h5 class="titleColor">LocalStorage</h5>
                        <section class="row" v-for="(value, key) in localStorage">
                            <section class="col" col-4>
                                <strong>{{key}}</strong>
                            </section>
                            <section class="col" col-8>{{value}}</section>
                        </section>
                        <h5 class="titleColor">SessionStorage</h5>
    
                        <section class="row" v-for="(value, key) in sessionStorage">
                            <section class="col" col-4>
                                <strong>{{key}}</strong>
                            </section>
                            <section class="col" col-8>{{value}}</section>
                        </section>
                        <h5 class="titleColor">CookieString</h5>
                        <section class="row">
                            <section class="col">{{cookieString}}</section>
                        </section>
                        <h5 class="titleColor">ParseCookie</h5>
                        <section class="row" v-for="(value, key) in cookieObject">
                            <section class="col" col-4>
                                <strong>{{key}}</strong>
                            </section>
                            <section class="col" col-8>{{value}}</section>
                        </section>
                        <h5 class="titleColor">操作</h5>
                        <section class="row">
                            <section class="col">
                                <button class="button" block @click="refreshStorageData">refresh</button>
                            </section>
                        </section>
                        <!--AlipayJSBridge/ap-->
                    </section>
                </div>
                <!-- webStorage info end-->
    
            </section>
        </article>
        <footer class="logBox__footer">
            <div class="logBox__footer--buttons left">
                <button :class="{'active':selectType==='console'}" @click="segmentClick('console')" class="log">
                    Console
                </button>
                <button v-if="isVimoExist" :class="{'active':selectType==='vimo'}" @click="segmentClick('vimo')" class="debug">
                    Vimo
                </button>
                <button :class="{'active':selectType==='storage'}" @click="segmentClick('storage')" class="info">
                    Storage
                </button>
            </div>
    
            <div class="logBox__footer--buttons right">
                <button class="close" @click="close()">close</button>
            </div>
        </footer>
        <aside class="logBox__aside" @click="open()">
            <span>OPEN</span>
        </aside>
    </section>
</template>
<script type="text/javascript">
var JsScroll = require('./iscroll.js')
export default {
  name: 'LogBox',
  data () {
    return {
      jsScrollInstance: null,         // js滚动的实例，iscroll实例
      thisKeysOfRegisterMethod: [],   // 已注册的方法列表
      platform: null,                 // platform信息
      config: null,                   // config信息
      isPlatformReady: false,              // 平台是否初始化完毕
      cachedConfig: [],
      settingConfig: [],

      isActive: false, // 是否打开
      selectType: 'console', // 默认选中全部
      recordList: [],
      isVimoExist: false, // vimo是否存在

      localStorage: {},
      sessionStorage: {},
      cookieObject: {},
      cookieString: ''
    }
  },
  computed: {
    listBoxElement () {
      return this.$refs.listBox
    }
  },
  methods: {
        /**
         * 打开盒子
         * @private
         * */
    open () {
      this.isActive = true
      this.refresh()
    },

        /**
         * 关闭盒子
         * @private
         * */
    close () {
      this.isActive = false
    },

        /**
         * 刷新iscroll
         * @private
         * */
    refresh () {
      this.$nextTick(() => {
        this.jsScrollInstance && this.jsScrollInstance.refresh()
      })
    },

        /**
         * @private
         * */
    setRecordList (list) {
      this.recordList = list
      this.refresh()
      if (this.selectType === 'console') {
        this.scrollToBottom()
      }
    },

    record (item) {
      this.recordList.push(item)
      this.refresh()
      if (this.selectType === 'console') {
        this.scrollToBottom()
      }
    },

    clear () {
      this.recordList = []
      this.refresh()
    },

        /**
         * @private
         * 点击segment按钮切换不同type
         * @param {string} type - all表示全部
         * */
    segmentClick (type) {
      this.selectType = type.toString().toLowerCase()
      this.refresh()
      if (this.selectType === 'console') {
        this.scrollToBottom()
      } else {
        this.scrollToTop()
      }
    },

        /**
         * @private
         * 获取类型的颜色Class
         * @param {string} type - 类型
         * */
    setTypeClass (type) {
      if (type) {
        return type.toString().toLowerCase()
      } else {
        return 'error'
      }
    },

        /**
         * @private
         * 滚动到底部
         * 执行需要等待DOM更新为完毕
         * */
    scrollToBottom () {
      this.$nextTick(() => {
        this.jsScrollInstance && this.jsScrollInstance.scrollTo(0, this.jsScrollInstance.wrapperHeight - this.jsScrollInstance.scrollerHeight, 300)
      })
    },

        /**
         * @private
         * 滚动到底部
         * 执行需要等待DOM更新为完毕
         * */
    scrollToTop () {
      this.$nextTick(() => {
        this.jsScrollInstance && this.jsScrollInstance.scrollTo(0, 0, 300)
      })
    },

    initJsScroll () {
      if (typeof JsScroll !== 'undefined') {
                // js滚动
        this.jsScrollInstance = new JsScroll(this.listBoxElement, {
          bounce: true,
          bindToWrapper: true,
          mouseWheel: true,
          scrollbars: false
        })
      } else {
        alert('JsScroll 未定义')
      }
    },
    refreshVimoData () {
      this.getVimoData()
      alert('DONE')
    },

        /**
         * 初始化Vimo相关
         * */
    getVimoData () {
            // 等待vimo初始化
      const init = () => {
        this.isVimoExist = true

        if (document.removeEventListener) {
          document.removeEventListener('VimoReady', init)
        }

        if (window.VM) {
          window.VM.platform.ready().then(() => {
            this.isPlatformReady = true
          })

          this.platform = window.VM.platform

          this.cachedConfig = window.VM.config._c
          this.settingConfig = window.VM.config._s

          this.thisKeysOfRegisterMethod = Object.keys(window.VM.platform.registerMethod())

          this.refresh()
        }
      }

      if (typeof window.VM === 'undefined') {
        if (document.addEventListener) {
          document.addEventListener('VimoReady', init, false)
        }
      } else {
        init()
      }
    },

        /**
         * 初始化存储相关信息
         * */
    initStorage () {
      this.localStorage = window.localStorage
      this.sessionStorage = window.sessionStorage
      this.cookieString = window.document.cookie
      var arrStr = document.cookie.split('; ')
      var obj = {}
      for (var i = 0; i < arrStr.length; i++) {
        var temp = arrStr[i].split('=')
                // 第一个是key, 其余的复原
        obj[temp.shift()] = temp.join('=')
      }
      this.cookieObject = obj
    },

    refreshStorageData () {
      this.initStorage()
      this.refresh()
      alert('DONE')
    }

  },
  mounted () {
    this.initJsScroll()
    this.getVimoData()
    this.initStorage()
  }
}
// Date Format
window.Date.prototype.Format = function Format (fmt) { // author: meizz
  var o = {
    'M+': this.getMonth() + 1, // 月份
    'd+': this.getDate(), // 日
    'h+': this.getHours(), // 小时
    'm+': this.getMinutes(), // 分
    's+': this.getSeconds(), // 秒
    'q+': Math.floor((this.getMonth() + 3) / 3), // 季度
    'S': this.getMilliseconds() // 毫秒
  }
  if (/(y+)/.test(fmt)) {
    fmt = fmt.replace(RegExp.$1, (this.getFullYear() + '').substr(4 - RegExp.$1.length))
  }
  for (var k in o) {
    if (new RegExp('(' + k + ')').test(fmt)) {
      fmt = fmt.replace(RegExp.$1, (RegExp.$1.length === 1) ? (o[k]) : (('00' + o[k]).substr(('' + o[k]).length)))
    }
  }
  return fmt
}
</script>
<style scoped lang="scss">
    $log: #757575;
    $debug: #36a8ee;
    $info: #32b208;
    $warn: #ff8f0e;
    $error: #ff0000;
    $assert: #ff0000;
    
    .logBox {
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        background-color: rgba(0, 0, 0, 0.8);
        color: #d5d5d5;
        z-index: 99999;
        display: flex;
        justify-content: flex-start;
        align-items: flex-start;
        flex-direction: column;
        font-size: 12px;
        transform: translateX(-100%);
        transition: all ease 200ms;
        &.active {
            transform: translateX(0);
        }
        .logBox__footer {
            flex: 0 1 46px;
            height: 46px;
            width: 100%;
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 0 2px;
            color: #fff;
            z-index: 9;
    
            background: #333;
            .logBox__footer--buttons {
                height: 80%;
                display: flex;
                justify-content: flex-start;
                align-items: center;
                button {
                    margin: 0 1px;
                    flex: 1;
                    height: 100%;
                    text-transform: uppercase;
                    background-color: transparent;
                    border: 0;
                    outline: none;
                    color: #fff;
                    padding: 0 10px;
                    box-sizing: border-box;
                    transition: all ease 200ms;
                    font-size: 12px;
    
                    &.all {
                        border: 1px solid #fff;
                        color: #fff;
                        border-radius: 2px;
                    }
                    &.all.active {
                        border: 1px solid #fff;
                        background-color: #fff;
                        color: #000;
                        border-radius: 2px;
                    }
    
                    &.log {
                        border: 1px solid $log;
                        color: $log;
                        border-radius: 2px;
                    }
                    &.log.active {
                        border: 1px solid $log;
                        background-color: $log;
                        color: #fff;
                        border-radius: 2px;
                    }
    
                    &.debug {
                        border: 1px solid $debug;
                        color: $debug;
                        border-radius: 2px;
                    }
                    &.debug.active {
                        border: 1px solid $debug;
                        background-color: $debug;
                        color: #fff;
                        border-radius: 2px;
                    }
    
                    &.info {
                        border: 1px solid $info;
                        color: $info;
                        border-radius: 2px;
                    }
                    &.info.active {
                        border: 1px solid $info;
                        background-color: $info;
                        color: #fff;
                        border-radius: 2px;
                    }
    
                    &.warn {
                        border: 1px solid $warn;
                        color: $warn;
                        border-radius: 2px;
                    }
                    &.warn.active {
                        border: 1px solid $warn;
                        background-color: $warn;
                        color: #fff;
                        border-radius: 2px;
                    }
    
                    &.error {
                        border: 1px solid $error;
                        color: $error;
                        border-radius: 2px;
                    }
                    &.error.active {
                        border: 1px solid $error;
                        background-color: $error;
                        color: #fff;
                        border-radius: 2px;
                    }
    
                    &.assert {
                        border: 1px solid $assert;
                        color: $assert;
                        border-radius: 2px;
                    }
                    &.assert.active {
                        border: 1px solid $assert;
                        background-color: $assert;
                        color: #fff;
                        border-radius: 2px;
                    }
                }
            }
        }
        .logBox__article {
            flex: 1;
            overflow: hidden;
            width: 100%;
            /*-webkit-overflow-scrolling: touch;*/
            .recordList {
                padding: 0 5px;
                position: absolute;
                top: 0;
                width: 100%;
                overflow: hidden;
                .recordItem {
                    padding: 5px 0;
                    display: flex;
                    justify-content: flex-start;
                    align-items: flex-start;
                    &.log {
                        .recordItem__info--names {
                            .type {
                                background-color: $log;
                            }
                            .name {
                                color: $log;
                            }
                        }
                    }
                    &.debug {
                        .recordItem__info--names {
                            .type {
                                background-color: $debug;
                            }
                            .name {
                                color: $debug;
                            }
                        }
                    }
                    &.info {
                        .recordItem__info--names {
                            .type {
                                background-color: $info;
                            }
                            .name {
                                color: $info;
                            }
                        }
                    }
                    &.warn {
                        .recordItem__info--names {
                            .type {
                                background-color: $warn;
                            }
                            .name {
                                color: $warn;
                            }
                        }
                    }
                    &.error {
                        .recordItem__info--names {
                            .type {
                                background-color: $error;
                            }
                            .name {
                                color: $error;
                            }
                        }
                    }
                    &.assert {
                        .recordItem__info--names {
                            .type {
                                background-color: $assert;
                            }
                            .name {
                                color: $assert;
                            }
                        }
                    }
                    .recordItem__count {
                        flex: 0 0 25px;
                        overflow: hidden;
                        display: flex;
                        justify-content: center;
                        align-items: center;
                    }
                    .recordItem__info {
                        flex: 1;
                        padding: 0 2px;
                        .recordItem__info--names {
                            color: #fff;
                            .type {
                                font-weight: bold;
                                text-transform: uppercase;
                                border-radius: 2px;
                                padding: 0 2px;
                            }
                            .name {
                                font-weight: bold;
                            }
                            .time {
                                color: #fff;
                            }
                        }
                        .recordItem__info--position {
                            .name {
                                color: #fff;
                                text-transform: capitalize;
                                font-weight: bold;
                            }
                        }
                        .recordItem__info--message {
                            font-size: 12px;
                            white-space: pre-wrap;
                            overflow: hidden;
                            .name {
                                color: #fff;
                                text-transform: capitalize;
                                font-weight: bold;
                            }
                        }
                        .recordItem__info--stack {
                            font-size: 12px;
                            .name {
                                color: #fff;
                                text-transform: capitalize;
                                font-weight: bold;
                            }
                        }
                    }
                }
            }
        }
        .logBox__aside {
            position: absolute;
            bottom: 0;
            right: 0;
            transform: translateX(100%);
            height: 46px;
            width: 46px;
            box-sizing: border-box;
            background: rgba(0, 0, 0, 0.8);
            text-transform: uppercase;
            display: flex;
            justify-content: center;
            align-items: center;
            flex-wrap: wrap;
            line-height: 130%;
            flex-direction: column;
            cursor: pointer;
        }
    }
    
    .isReady {
        color: $info;
    }
    
    .isNotReady {
        color: $warn;
    }
    
    .titleColor {
        font-size: 16px;
        margin: 14px 0;
        color: $debug;
    }
    
    .button-md,
    .button-ios {
        background-color: $debug;
    }
    
    .grid {
        padding: 5px;
        margin-left: auto;
        margin-right: auto;
        width: 100%;
        .row {
            padding: 5px;
            margin-left: auto;
            margin-right: auto;
            width: 100%;
            display: flex;
            flex-direction: row;
            flex-wrap: nowrap;
            .col {
                position: relative;
                padding: 5px;
                width: 100%;
                margin: 0;
                min-height: 1px;
                -webkit-flex-basis: 0;
                -ms-flex-preferred-size: 0;
                flex-basis: 0;
                -webkit-box-flex: 1;
                -webkit-flex-grow: 1;
                -ms-flex-positive: 1;
                flex-grow: 1;
                max-width: 100%;
    
                &[col-4] {
                    -webkit-box-flex: 0;
                    -webkit-flex: 0 0 33.33333%;
                    -ms-flex: 0 0 33.33333%;
                    flex: 0 0 33.33333%;
                    width: 33.33333%;
                    max-width: 33.33333%;
                    padding: 5px;
    
                    white-space: nowrap;
                    overflow: hidden;
                    text-overflow: ellipsis;
                }
                &[col-8] {
                    flex: 0 0 66.66667%;
                    width: 66.66667%;
                    max-width: 66.66667%;
                    padding: 5px;
    
                    overflow: hidden;
                    text-overflow: ellipsis;
                    display: -webkit-box;
                    -webkit-line-clamp: 5; //显示的行数
                    -webkit-box-orient: vertical;
                    line-height: 168%;
                }
            }
        }
    }
    
    .button {
        position: relative;
        z-index: 0;
        text-align: center;
        text-overflow: ellipsis;
        text-transform: none;
        white-space: nowrap;
        cursor: pointer;
        vertical-align: top;
        transition: background-color, opacity 100ms linear;
        font-kerning: none;
        user-select: none;
        text-decoration: none;
        display: block;
        clear: both;
        contain: strict;
        margin-right: 0;
        margin-left: 0;
        height: 36px;
    
        border: 1px solid $warn;
        background-color: $warn;
        color: #fff;
        border-radius: 2px;
        font-size: 12px;
    
        text-transform: uppercase;
    }
</style>
