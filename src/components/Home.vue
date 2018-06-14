<template>
  <div class="home_container">
    <img :src="images.localId[0]" alt="" v-if="images.localId.length > 0" class="image_target">
    <button class="btn_item" @click="chooseFromAlbum">从相册选择</button>
  </div>
</template>

<script>
const axios = require('axios')
const qs = require('querystring')
export default {
  name: 'Home',
  data () {
    return {
      at: '',
      wxConfig: {},
      images: {
        localId: [],
        serverId: []
      }
    }
  },
  async created () {
    let wxConfigData = await this.getWxConfig()
    if (wxConfigData.status === 200) {
      this.wxConfig = wxConfigData.data
      wx.config({
        debug: true, // 开启调试模式,调用的所有api的返回值会在客户端alert出来，若要查看传入的参数，可以在pc端打开，参数信息会通过log打出，仅在pc端时才会打印。
        appId: this.wxConfig.appId || '', // 必填，公众号的唯一标识
        timestamp: this.wxConfig.timestamp, // 必填，生成签名的时间戳
        nonceStr: this.wxConfig.nonceStr || '', // 必填，生成签名的随机串
        signature: this.wxConfig.signature || '',// 必填，签名
        jsApiList: [
          'checkJsApi',
          'onMenuShareTimeline',
          'onMenuShareAppMessage',
          'onMenuShareQQ',
          'onMenuShareWeibo',
          'onMenuShareQZone',
          'hideMenuItems',
          'showMenuItems',
          'hideAllNonBaseMenuItem',
          'showAllNonBaseMenuItem',
          'translateVoice',
          'startRecord',
          'stopRecord',
          'onVoiceRecordEnd',
          'playVoice',
          'onVoicePlayEnd',
          'pauseVoice',
          'stopVoice',
          'uploadVoice',
          'downloadVoice',
          'chooseImage',
          'previewImage',
          'uploadImage',
          'downloadImage',
          'getNetworkType',
          'openLocation',
          'getLocation',
          'hideOptionMenu',
          'showOptionMenu',
          'closeWindow',
          'scanQRCode',
          'chooseWXPay',
          'openProductSpecificView',
          'addCard',
          'chooseCard',
          'openCard'
        ] // 必填，需要使用的JS接口列表
      })
    }
    // let accessTokenData = await this.getAccessToken()
    // if (accessTokenData.status === 200) {
    //   this.at = accessTokenData.data.access_token
    // }
  },
  methods: {
    getAccessToken () {
      return new Promise(resolve => {
        axios.get('http://192.168.0.103:3000/wx/index/getAccessToken').then(({data}) => {
          resolve(data)
        })
      })
    },
    getWxConfig () {
      return new Promise(resolve => {
        axios.post('http://192.168.0.103:3000/wx/index/getWxConfig', qs.stringify({
          url: location.href
        })).then(({data}) => {
          resolve(data)
        })
      })
    },
    chooseFromAlbum () {
      const that = this
      wx.ready(function () {
        wx.chooseImage({
          count: 1, // 默认9
          sizeType: ['original', 'compressed'], // 可以指定是原图还是压缩图，默认二者都有
          sourceType: ['album', 'camera'], // 可以指定来源是相册还是相机，默认二者都有
          success: function (res) {
            that.images.localId = res.localIds
            alert('已选择了' + that.images.localId.length + '张')
          }
        })
      })
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.home_container {
  width: 100%;
  height: 100%;
}
  .btn_item {
    padding: 4px 8px;
    background-color: transparent;
    border: 1px solid #c8c8c8;
    border-radius: 4px;
    font-size: 13px;
    margin-top: 50px;
  }
  .btn_item:active {
    opacity: 0.7;
  }
  .image_target {
    max-width: 375px;
    max-height: 500px;
  }
</style>
