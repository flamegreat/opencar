<template>
  <div class="layui-header ws-header ws-bg-light">
    <div class="layui-container">
      <div class="ws-logo">
        <h1>OpenCar</h1>
      </div>
    </div>
  </div>

  <div class="layui-container ws-container">
    <div class="ws-content">
      <HomePage msg="opencar" />
    </div>
  </div>

  <div class="layui-footer ws-footer">
    <p>本站不上传、不提供任何内容，所有操作均在本地完成</p>
  </div>
</template>

<script>
import Home from './components/Home.vue'

export default {
  name: 'App',
  components: {
    HomePage: Home
  }
}
</script>

<style>
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
