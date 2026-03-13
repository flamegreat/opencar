<template>
  <div class="common-layout">
    <el-container>
      <el-header class="ws-header">
        <div class="ws-logo">
          <h1>OpenCar</h1>
        </div>
      </el-header>

      <!-- 主要功能 -->
      <el-main>
        <div class="ws-content">
          <div class="app-card">
            
            <el-upload 
              ref="uploadRef" 
              class="upload-demo" 
              drag 
              action="https://jsonplaceholder.typicode.com/posts/"
              :on-change="handleChange" 
              :auto-upload="false" 
              :on-exceed="handleExceed" 
              :limit="1"
              :show-file-list="false"
            >
              <el-icon class="upload-icon"><UploadFilled /></el-icon>
              <div class="el-upload__text">将文件拖到此处，或 <em>点击上传</em></div>
              <div v-if="selectedFile" class="selected-file">
                <el-tag type="success" effect="dark">{{ selectedFile.name }}</el-tag>
              </div>
            </el-upload>
            
            <div class="button-group">
              <el-button type="primary" @click="reverseAndSave()" size="large">
                <el-icon><Download /></el-icon> 转换并下载
              </el-button>
              <el-button type="success" @click="reverseAndPlay()" size="large">
                <el-icon><VideoPlay /></el-icon> 预览（图片/视频）
              </el-button>
            </div>
            
            <div class="video-container">
              <video 
                id="video" 
                :src="video_url" 
                controls 
                muted="true" 
                @error="player_error"
                @loadedmetadata="player_loadedmetadata"
              ></video>
              <img id="img" @error="player_error" />
            </div>
          </div>
        </div>
      </el-main>

      <el-footer height="40px" class="ws-footer">
        <p>本站不提供任何文件资源，所有操作均在本地完成</p>
      </el-footer>
    </el-container>
  </div>
</template>

<script>

import 'element-plus/theme-chalk/display.css'
import { UploadFilled, Download, VideoPlay } from '@element-plus/icons-vue'

export default {
  name: 'OpenCar',
  components: {
    UploadFilled,
    Download,
    VideoPlay
  },
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
          const blob = new Blob([arrayBuffer], { type: file.type });

          const video = document.getElementById('video');
          const img = document.getElementById('img');
          const url = URL.createObjectURL(blob);
          if (file.type.includes('video')) {
            img.style.display = 'none';
            video.style.display = '';
            video.src = url;
          }
          else if (file.type.includes('image')) {
            video.style.display = 'none';
            img.style.display = '';
            img.src = url;
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
          const blob = new Blob([reversedArray], { type: file.type });

          const a = document.createElement('a');
          a.href = URL.createObjectURL(blob);
          a.download = file.name;
          a.click();
        };
        reader.readAsArrayBuffer(file);
      }
    },
    // 是否转换并播放
    reverseAndPlay(reverse = false) {
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

          const blob = new Blob([arrayBuffer], { type: file.type });

          const video = document.getElementById('video');
          const img = document.getElementById('img');
          const url = URL.createObjectURL(blob);
          const container = document.querySelector('.video-container');
          if (file.type.includes('video')) {
            img.style.display = 'none';
            video.style.display = '';
            video.src = url;
            video.play().catch(err => console.error('播放失败:', err));
            container.style.backgroundColor = '#000';
          }
          else if (file.type.includes('image')) {
            video.style.display = 'none';
            img.style.display = '';
            img.src = url;
            container.style.backgroundColor = 'transparent';
          } else {
            console.log('不支持的文件类型:', file.type);
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
/* 基础样式 */
.common-layout {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  background: #f5f5f5;
}

:deep(.el-container) {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  width: 100%;
  border: none;
  outline: none;
  background: none;
}

.ws-header {
  display: flex;
  justify-content: center;
  background: #4facfe;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.15);
  height: 60px;
  width: 100%;
  padding: 0 20px;
  box-sizing: border-box;
  position: static;
  border: none;
  outline: none;
}

:deep(.el-header) {
  border: none;
  outline: none;
  margin: 0;
  padding: 0;
  height: 60px;
}

.ws-header .ws-logo h1 {
  margin: 0;
  font-size: 1.25rem;
  font-weight: bold;
  color: white;
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  line-height: 60px;
}

.ws-content {
  max-width: 800px;
  margin: 0 auto;
  padding: 30px 20px;
  width: 100%;
  display: flex;
  align-items: center;
  justify-content: center;
}

:deep(.el-main) {
  flex: 1;
  padding: 0;
  margin: 0;
  border: none;
  outline: none;
  background: none;
}

.app-card {
  width: 100%;
  background: white;
  border-radius: 12px;
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.1);
  padding: 30px;
}

.app-title {
  text-align: center;
  color: #333;
  font-size: 1.5rem;
  margin-bottom: 10px;
  font-weight: 600;
}

.app-description {
  text-align: center;
  color: #666;
  margin-bottom: 30px;
  font-size: 1rem;
}

.upload-demo {
  border: 2px dashed #4facfe;
  border-radius: 12px;
  padding: 20px 20px;
  text-align: center;
  margin-bottom: 30px;
  background: #f8faff;
  position: relative;
}

.upload-icon {
  font-size: 2rem;
  color: #4facfe;
  margin-bottom: 10px;
}

.el-upload__text {
  font-size: 1rem;
  color: #606266;
  margin-bottom: 5px;
  line-height: 1.2;
  font-weight: 500;
  letter-spacing: 0.5px;
}

.el-upload__text em {
  color: #4facfe;
  font-weight: 500;
}

.selected-file {
  margin-top: 20px;
  padding: 10px 15px;
  background-color: rgba(79, 172, 254, 0.1);
  border-radius: 8px;
  display: inline-block;
  font-size: 1rem;
  font-weight: 500;
  letter-spacing: 0.5px;
}

.button-group {
  display: flex;
  justify-content: center;
  gap: 15px;
  margin-bottom: 30px;
  flex-wrap: wrap;
}

.button-group .el-button {
  min-width: 180px;
  width: 180px;
  text-align: center;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
}

.el-button {
  border-radius: 50px;
  font-weight: 500;
  letter-spacing: 0.5px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  transition: all 0.3s ease;
}

.el-button .el-icon {
  font-size: 1.25rem;
}

.el-button:hover {
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.2);
  filter: brightness(1.05);
}

.el-button--primary {
  background: #4361ee;
  border: none;
}

.el-button--success {
  background: #4ade80;
  border: none;
}

.video-container {
  position: relative;
  width: 100%;
  padding-bottom: 56.25%;
  /* 16:9 宽高比 */
  margin-top: 20px;
  background-color: #000;
  border-radius: 10px;
  overflow: hidden;
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.2);
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
  width: 100%;
  height: 100%;
  object-fit: contain;
}

.ws-footer {
  display: flex;
  align-items: center;
  justify-content: center;
  background: #4facfe;
  color: white;
  font-size: 0.875rem;
  box-shadow: 0 -2px 10px rgba(0, 0, 0, 0.1);
}

.ws-footer p {
  margin: 0;
  text-align: center;
  padding: 0 10px;
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.1);
}

/* 移动端适配 */
@media screen and (max-width: 768px) {
  .ws-header {
    height: 60px !important;
  }

  :deep(.el-header) {
    height: 60px !important;
  }

  .ws-logo h1 {
    font-size: 1.1rem;
  }

  .ws-content {
    padding: 20px 15px;
  }

  .app-card {
    padding: 20px;
  }

  .app-title {
    font-size: 1.25rem;
  }

  .upload-demo {
    padding: 15px 15px;
  }

  .upload-icon {
    font-size: 1.5rem;
  }

  .el-upload__text {
    font-size: 0.875rem;
    font-weight: 500;
    letter-spacing: 0.5px;
  }

  .selected-file {
    padding: 8px 12px;
    font-size: 0.875rem;
    font-weight: 500;
    letter-spacing: 0.5px;
  }

  .button-group {
    gap: 10px;
  }

  .el-button {
    font-size: 0.875rem;
    padding: 8px 16px;
  }

  .ws-footer {
    height: 40px !important;
    font-size: 0.75rem;
  }
}

/* 小屏幕移动端适配 */
@media screen and (max-width: 480px) {
  .ws-header {
    height: 60px !important;
  }

  :deep(.el-header) {
    height: 60px !important;
  }

  .ws-logo h1 {
    font-size: 1rem;
  }
  .ws-content {
    padding: 15px 10px;
  }

  .app-card {
    padding: 15px;
  }

  .app-title {
    font-size: 1.1rem;
  }

  .app-description {
    font-size: 0.875rem;
  }

  .upload-demo {
    padding: 30px 10px;
  }

  .upload-icon {
    font-size: 2rem;
  }

  .el-upload__text {
    font-size: 0.875rem;
  }

  .button-group {
    flex-direction: column;
    align-items: center;
  }

  .button-group .el-button {
    font-size: 0.875rem;
    padding: 10px 20px;
    width: 100%;
    max-width: 180px;
  }

  .ws-footer p {
    font-size: 0.6875rem;
  }
}
</style>
