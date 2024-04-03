<template>
  <p>
    <label for="fileInput" class="custom-file-upload">选择文件</label>
    <input type="file" id="fileInput" @change="fileSelected">
    <span v-if="selectedFile"> 已选择文件：{{ selectedFile.name }}</span>
    <span v-else> 请选择文件</span>
  </p>
  <br />
  <p>
    <button @click="reverseAndSave()" class="layui-btn layui-btn-normal">转换并下载</button>
    <button @click="reverseAndPlay()" class="layui-btn layui-btn-normal">播放</button>
  </p>
  <br />
  <div class="video-container">
    <video id="video" :src="video_url" controls muted="true" @error="player_error"
      @loadedmetadata="player_loadedmetadata"></video>
    <img id="img" @error="player_error"/>
  </div>
</template>

<script>

export default {
  name: 'OpenCar',
  props: {
    msg: String
  },
  data() {
    return {
      selectedFile: null,
      video_url: null,
    };
  },
  methods: {
    // 文件选择事件
    fileSelected(event) {
      this.selectedFile = event.target.files[0];
      // this.play();
    },
    // 直接播放
    play() {
      const file = this.selectedFile;
      console.log(file);
      if (file) {
        const reader = new FileReader();
        reader.onload = async function (e) {
          const arrayBuffer = e.target.result;
          const blob = new Blob([arrayBuffer], { type: 'application/octet-stream' });

          if (file.type === 'video/mp4') {
            const video = document.getElementById('video');
            video.src = URL.createObjectURL(blob);
          }
          else if (file.type === 'image/png') {
            const img = document.getElementById('img');
            img.src = URL.createObjectURL(blob);
          }
        };
        reader.readAsArrayBuffer(file);
      }
    },
    // 转换并保存
    reverseAndSave() {
      const file = this.selectedFile;
      if (file) {
        const reader = new FileReader();
        reader.onload = async function (e) {
          const arrayBuffer = e.target.result;
          const uint8Array = new Uint8Array(arrayBuffer);
          const reversedArray = uint8Array.reverse();
          const blob = new Blob([reversedArray], { type: 'application/octet-stream' });

          const a = document.createElement('a');
          a.href = URL.createObjectURL(blob);
          a.download = file.name;
          a.click();
        };
        reader.readAsArrayBuffer(file);
      }
    },
    // 是否转换并播放
    reverseAndPlay(reverse) {
      const file = this.selectedFile;
      if (file) {
        const reader = new FileReader();
        reader.onload = async function (e) {
          var arrayBuffer;

          if (reverse) {
            const uint8Array = new Uint8Array(e.target.result);
            const reversedArray = uint8Array.reverse();
            arrayBuffer = reversedArray;
          } else {
            arrayBuffer = e.target.result;
          }

          const blob = new Blob([arrayBuffer], { type: 'application/octet-stream' });

          const video = document.getElementById('video');
          const img = document.getElementById('img');
          const url = URL.createObjectURL(blob);
          if (file.type.includes('video')) {
            img.style.display = 'none';
            video.style.display = '';
            video.src = url;
            video.play();
          }
          else if (file.type.includes('image')) {
            video.style.display = 'none';
            img.style.display = '';
            img.src = url;
          } else {
            console.log(file.type);
          }
        };
        reader.readAsArrayBuffer(file);
      }
    },
    player_error(e) {
      this.reverseAndPlay(true);
    },
    player_loadedmetadata(e) {
      // document.getElementById('video').play();
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.video-container {
  position: relative;
  width: 100%;
  padding-bottom: 56.25%;
  /* 16:9 宽高比 */
}

.video-container video {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
}

.video-container img {
  position: absolute;
  top: 0;
  left: 0;
  height: 100%;
}

input[type="file"] {
  display: none;
}

.custom-file-upload {
  border: 1px solid #ccc;
  border-radius: 2px;
  padding: 9px 18px;
  cursor: pointer;
}
</style>
