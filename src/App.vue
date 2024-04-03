<template>
  <div class="common-layout">
    <el-container>
      <el-header>
        <div class="layui-header ws-header ws-bg-light">
          <div class="layui-container">
            <div class="ws-logo">
              <h1>OpenCar</h1>
            </div>
          </div>
        </div>
      </el-header>

      <!-- 主要功能 -->
      <el-main>
        <div class="ws-content">
          <div class="ws-content">
            <p>
              <el-upload ref="uploadRef" class="upload-demo" drag action="https://jsonplaceholder.typicode.com/posts/"
                :on-change="handleChange" :auto-upload="false" :on-exceed="handleExceed" :limit="1"
                :show-file-list="false">

                <div class="el-upload__text">将文件拖到此处，或 <em>点击上传</em></div><br />
                <div v-if="selectedFile">当前选择：{{ selectedFile.name }}</div>
              </el-upload>
            </p>
            <p>
              <el-button type="primary" @click="reverseAndSave()">转换并下载</el-button>
              <el-button type="primary" @click="reverseAndPlay()">播放</el-button>
            </p>
            <div class="video-container">
              <video id="video" :src="video_url" controls muted="true" @error="player_error"
                @loadedmetadata="player_loadedmetadata"></video>
              <img id="img" @error="player_error" />
            </div>
          </div>
        </div>
      </el-main>

      <el-footer>
        <div class="layui-footer ws-footer">
          <p>本站不上传、不提供任何内容，所有操作均在本地完成</p>
        </div>
      </el-footer>
    </el-container>
  </div>


</template>

<script>

import 'element-plus/theme-chalk/display.css'

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
    handleChange(file) {
      // 处理文件
      this.selectedFile = file.raw;
    },
    handleExceed(files) {
      // 处理文件
      this.selectedFile = files[0];
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
</style>
