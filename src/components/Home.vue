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
    <button @click="reverseAndPlay()" class="layui-btn layui-btn-normal">转换并播放</button>
    <button @click="play()" class="layui-btn layui-btn-normal">播放</button>
  </p>
  <br />
  <div class="video-container">
    <video id="video" :src="video_url" controls muted="true"></video>
    <video-player ref="vueMiniPlayer" />
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
    fileSelected(event) {
      this.selectedFile = event.target.files[0];
    },
    // 直接播放
    play() {
      const file = this.selectedFile;
      if (file) {
        const reader = new FileReader();
        reader.onload = async function (e) {
          const arrayBuffer = e.target.result;
          const blob = new Blob([arrayBuffer], { type: 'application/octet-stream' });

          console.log(this.$refs);
          // this.$refs.vueMiniPlayer.src = URL.createObjectURL(blob);
          // this.$refs.vueMiniPlayer.play(); // 播放视频

          const video = document.getElementById('video');
          video.src = URL.createObjectURL(blob);
          video.poster = URL.createObjectURL(blob);
          video.play();
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
    // 转换并播放
    reverseAndPlay() {
      const video = document.getElementById('video');
      const file = this.selectedFile;
      if (file) {
        const reader = new FileReader();
        reader.onload = async function (e) {
          const arrayBuffer = e.target.result;
          const uint8Array = new Uint8Array(arrayBuffer);
          const reversedArray = uint8Array.reverse();
          const blob = new Blob([reversedArray], { type: 'application/octet-stream' });

          video.src = URL.createObjectURL(blob);
          video.poster = URL.createObjectURL(blob);
          video.play();
        };
        reader.readAsArrayBuffer(file);
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>
