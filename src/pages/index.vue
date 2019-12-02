<template>
  <div class="container">
    <img src="../assets/bg.png" class="bg-img" alt="">
    <video id="videoCamera" autoplay></video>
  </div>
</template>
  <script>
  export default {
    data () {
      return {
        // videoWidth: 420,
        // videoHeight: 265,
        imgSrc: '',
        thisCancas: null,
        thisContext: null,
        thisVideo: null,
      }
    },
    mounted(){
      this.getCompetence()
    },
    methods: {
// 调用权限（打开摄像头功能）
      getCompetence () {
        var _this = this
        // this.thisCancas = document.getElementById('canvasCamera')
        // this.thisContext = this.thisCancas.getContext('2d')
        this.thisVideo = document.getElementById('videoCamera')
        // 旧版本浏览器可能根本不支持mediaDevices，我们首先设置一个空对象
        if (navigator.mediaDevices === undefined) {
          navigator.mediaDevices = {}
        }
        // 一些浏览器实现了部分mediaDevices，我们不能只分配一个对象
        // 使用getUserMedia，因为它会覆盖现有的属性。
        // 这里，如果缺少getUserMedia属性，就添加它。
        if (navigator.mediaDevices.getUserMedia === undefined) {
          navigator.mediaDevices.getUserMedia = function (constraints) {
            // 首先获取现存的getUserMedia(如果存在)
            var getUserMedia = navigator.webkitGetUserMedia || navigator.mozGetUserMedia || navigator.getUserMedia
            // 有些浏览器不支持，会返回错误信息
            // 保持接口一致
            if (!getUserMedia) {
              return Promise.reject(new Error('getUserMedia is not implemented in this browser'))
            }
            // 否则，使用Promise将调用包装到旧的navigator.getUserMedia
            return new Promise(function (resolve, reject) {
              getUserMedia.call(navigator, constraints, resolve, reject)
            })
          }
        }
        // { width: this.videoWidth, height: this.videoHeight, transform: 'scaleX(-1)' }
        var constraints = { audio: false, video: true }
        navigator.mediaDevices.getUserMedia(constraints).then(function (stream) {
          // 旧的浏览器可能没有srcObject
          if ('srcObject' in _this.thisVideo) {
            _this.thisVideo.srcObject = stream
          } else {
            // 避免在新的浏览器中使用它，因为它正在被弃用。
            _this.thisVideo.src = window.URL.createObjectURL(stream)
          }
          _this.thisVideo.onloadedmetadata = function (e) {
            _this.thisVideo.play()
          }
        }).catch(err => {
          console.log(err)
        })
      },

// 关闭摄像头
      stopNavigator () {
        this.thisVideo.srcObject.getTracks()[0].stop()
      }
    },

  }
   
  </script>
  <style scoped>
  *{
    margin: 0;
    padding: 0;
    box-sizing: border-box;
  }
  .container{
    width: 100vw;
    height: 100vh;
    overflow: hidden;
    position: relative;
  }
  .bg-img{
    position: fixed;
    width: 100%;
    height: 100%;
    top: 0;
    left: 0;
  }
 video{
    position: absolute;
    width: 21.8%;
    height: 24.8%;
    top: 13vh;
    right: 0px;
  }
  </style>