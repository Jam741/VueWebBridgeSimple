<template>
  <div id="app" style="display: flex;flex-direction:column" class="mainBox">

    <h1>{{ msg }}</h1>

    <button @click="hideHeader">藏Header</button>
    <button @click="showHeader">显示Header</button>
    <button @click="setTitle">设置标题</button>
    <button @click="setBackText">设置返回键文本</button>
    <button @click="openNewWebView">打开一个新的WebView页面</button>
    <button @click="startLogin">跳转到登录页面</button>
    <button @click="startScan">打开扫描二维码功能</button>
    <button @click="callPhone">拨打电话</button>
    <button @click="downloadPic">下载图片</button>
  </div>
</template>


<script>

  // import "config/jsbridge"

  const JsBridgeCode = {
    "CODE_LUANCH_MEETING_BLT": 10001,
    "CODE_LUANCH_MEETING_UCC": 10002,
    "CODE_LUANCH_SCAN_MODULE": 10003,
    "CODE_LUANCH_LOGIN_MODULE": 10004,
    "CODE_OPEN_WEBVIEW": 10005,
    "CODE_SHOW_TOPBAR": 10006,
    "CODE_SET_TOPBAR_TITLE": 10007,
    "CODE_SET_BACK_TEXT": 10008,
    "CODE_CALL": 10009,
    "CODE_DOWNLOAD_FILE": 10010,
  };


  export default {
    name: 'app',
    data() {
      return {
        msg: 'Welcome to Your Vue.js App',
        isAndroid: true,
      }
    },
    created() {

      window.scanResult = function (res) {
        alert("二维码信息：" + res.data)
      };

      window.defaultCallback = function (res) {
        alert("回调data:" + res.data)
      };

      window.currentLocationHandler = function (jsonRes) {
        console.log(jsonRes)
      };

      if (!this.isAndroid) {
        this.connectWebViewJavascriptBridge(function (bridge) {
          bridge.init(function (message, responseCallback) {
            let data = {'Javascript Responds': 'Wee!'};
            responseCallback(data)
          });

          bridge.registerHandler('invokeNative', function (res) {

          })
        })
      }

    },
    methods: {

      connectWebViewJavascriptBridge(callback) {
        if (window.WebViewJavascriptBridge) {
          callback(window.WebViewJavascriptBridge)
        } else {
          document.addEventListener('WebViewJavascriptBridgeReady', function () {
            callback(window.WebViewJavascriptBridge)
          }, false)
        }
      },


      showHeader() {
        var jsonParamter = {
          "code": JsBridgeCode.CODE_SHOW_TOPBAR,
          "data": true,
          "callback": "defaultCallback"
        };
        this.invokeNative(jsonParamter)
      },

      hideHeader() {
        var jsonParamter = {
          "code": JsBridgeCode.CODE_SHOW_TOPBAR,
          "data": false,
          "callback": "defaultCallback"
        };
        this.invokeNative(jsonParamter)
      },

      setTitle() {
        var jsonParamter = {
          "code": JsBridgeCode.CODE_SET_TOPBAR_TITLE,
          "data": "New title",
          "callback": "defaultCallback"
        };
        this.invokeNative(jsonParamter)
      },
      setBackText() {
        var jsonParamter = {
          "code": JsBridgeCode.CODE_SET_BACK_TEXT,
          "data": "New title",
          "callback": "defaultCallback"
        };
        this.invokeNative(jsonParamter)
      },

      openNewWebView() {
        var jsonParamter = {
          "code": JsBridgeCode.CODE_OPEN_WEBVIEW,
          "data": {
            "url": "http://192.168.24.197:8080/",
            "hasTopBar": true,
            "backText": "关闭",
            "topTitleText": "新页面"
          },
          "callback": "defaultCallback"
        };
        this.invokeNative(jsonParamter)
      },


      startLogin() {
        var jsonParamter = {
          "code": JsBridgeCode.CODE_LUANCH_LOGIN_MODULE,
          "callback": "defaultCallback"
        };
        this.invokeNative(jsonParamter)
      },

      startScan() {
        var jsonParamter = {
          "code": JsBridgeCode.CODE_LUANCH_SCAN_MODULE,
          "callback": "scanResult"
        };
        this.invokeNative(jsonParamter)
      },

      callPhone() {
        var jsonParamter = {
          "code": JsBridgeCode.CODE_CALL,
          "data": "13888888888",
          "callback": "defaultCallback"
        };
        this.invokeNative(jsonParamter)
      },

      downloadPic() {
        var jsonParamter = {
          "code": JsBridgeCode.CODE_DOWNLOAD_FILE,
          "data": "http://pic1.win4000.com/wallpaper/2017-12-19/5a387cbbd1eae.jpg",
          "callback": "defaultCallback"
        };
        this.invokeNative(jsonParamter)
      },

      invokeNative(jsonParameter) {
        console.log(JSON.stringify(jsonParameter));
        if (this.isAndroid) {
          window.jsBridge.invokeNative(JSON.stringify(jsonParameter))
        } else {
          if (window.WebViewJavascriptBridge) {
            window.WebViewJavascriptBridge.callHandler('invokeNative', jsonParameter, function (response) {
            });
          }
        }
      }
    }
  }
</script>

<style lang="scss">
  #app {
    font-family: 'Avenir', Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }

  h1, h2 {
    font-weight: normal;
  }

  ul {
    list-style-type: none;
    padding: 0;
  }

  li {
    display: inline-block;
    margin: 0 10px;
  }

  a {
    color: #42b983;
  }

  .mainBox {

  }

  .mainBox button {
    margin: 0.5rem;
    font-size: 1.5rem;
    padding: 1.5rem 0;
  }
</style>
