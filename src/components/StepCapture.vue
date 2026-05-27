<template>
  <div class="capture-container">
    <h2 class="title">Phòng Chụp Ảnh</h2>
    
    <div class="viewport-wrapper" :class="isPortrait ? 'portrait-mode' : 'landscape-mode'">
      <video ref="videoRef" autoplay playsinline class="camera-stream" :class="{ 'flash-effect': isFlashing }"></video>
      <canvas ref="canvasRef" :width="isPortrait ? 960 : 1280" :height="isPortrait ? 1280 : 960" style="display: none;"></canvas>
      
      <div v-if="countdown > 0" class="countdown-display">
        <span class="count-num">{{ countdown }}</span>
      </div>

      <div class="indicator">
        Tiến độ: {{ localImages.length }} / {{ expectedCount }}
      </div>
    </div>

    <div class="actions">
      <button v-if="!isCapturing" class="btn-capture" @click="startSequence">Bắt đầu chụp tự động</button>
      <button v-if="!isCapturing" class="btn-back" @click="handleBack">Quay lại</button>
      <div v-else class="capturing-text">Chuẩn bị đổi dáng liên tục nhé! ✨</div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue';

const props = defineProps({
  expectedCount: {
    type: Number,
    required: true
  }
});

const emit = defineEmits(['complete', 'back']);

const videoRef = ref(null);
const canvasRef = ref(null);
const stream = ref(null);
const countdown = ref(0);
const isCapturing = ref(false);
const isFlashing = ref(false);
const localImages = ref([]);
const isPortrait = ref(false);

const checkOrientation = () => {
  isPortrait.value = window.innerHeight > window.innerWidth && window.innerWidth < 768;
};

onMounted(async () => {
  checkOrientation();
  window.addEventListener('resize', checkOrientation);
  
  try {
    stream.value = await navigator.mediaDevices.getUserMedia({
      video: { 
        width: { ideal: 1280 }, 
        height: { ideal: 960 },
        facingMode: "user"
      }
    });
    if (videoRef.value) {
      videoRef.value.srcObject = stream.value;
    }
  } catch (err) {
    alert('Vui lòng cấp quyền truy cập camera để tiếp tục.');
    emit('back');
  }
});

const startSequence = async () => {
  isCapturing.value = true;
  localImages.value = [];

  for (let i = 0; i < props.expectedCount; i++) {
    countdown.value = 3;
    await new Promise((resolve) => {
      const timer = setInterval(() => {
        countdown.value--;
        if (countdown.value === 0) {
          clearInterval(timer);
          resolve();
        }
      }, 1000);
    });

    triggerFlash();
    captureShot();
    await new Promise((resolve) => setTimeout(resolve, 1200));
  }

  stopCamera();
  emit('complete', localImages.value);
};

const triggerFlash = () => {
  isFlashing.value = true;
  setTimeout(() => {
    isFlashing.value = false;
  }, 150);
};

const captureShot = () => {
  if (videoRef.value && canvasRef.value) {
    const ctx = canvasRef.value.getContext('2d');
    const cw = canvasRef.value.width;
    const ch = canvasRef.value.height;
    const vWidth = videoRef.value.videoWidth;
    const vHeight = videoRef.value.videoHeight;
    
    ctx.clearRect(0, 0, cw, ch);
    
    let drawW, drawH, drawX, drawY;
    const videoRatio = vWidth / vHeight;
    const canvasRatio = cw / ch;
    
    if (videoRatio > canvasRatio) {
      drawH = ch; drawW = ch * videoRatio; drawX = (cw - drawW) / 2; drawY = 0;
    } else {
      drawW = cw; drawH = cw / videoRatio; drawX = 0; drawY = (ch - drawH) / 2;
    }
    const slimFactor = 0.94;
    const slimW = drawW * slimFactor;
    const slimX = drawX + (drawW - slimW) / 2;

    const rawCanvas = document.createElement('canvas');
    rawCanvas.width = cw; rawCanvas.height = ch;
    const rawCtx = rawCanvas.getContext('2d');
    rawCtx.translate(cw, 0); rawCtx.scale(-1, 1);
    rawCtx.drawImage(videoRef.value, slimX, drawY, slimW, drawH);

    const beautyCanvas = document.createElement('canvas');
    beautyCanvas.width = cw; beautyCanvas.height = ch;
    const beautyCtx = beautyCanvas.getContext('2d');
    beautyCtx.filter = 'brightness(1.58) contrast(0.72) saturate(1.25) blur(7px)';
    beautyCtx.drawImage(rawCanvas, 0, 0);
    beautyCtx.filter = 'none';

    const binaryMaskCanvas = document.createElement('canvas');
    binaryMaskCanvas.width = cw; binaryMaskCanvas.height = ch;
    const binaryMaskCtx = binaryMaskCanvas.getContext('2d');
    binaryMaskCtx.drawImage(rawCanvas, 0, 0);
    
    const imgData = binaryMaskCtx.getImageData(0, 0, cw, ch);
    const data = imgData.data;
    
    for (let i = 0; i < data.length; i += 4) {
      const r = data[i]; const g = data[i+1]; const b = data[i+2];
      const isSkin = (r > 45 && g > 35 && b > 20 && r > g && r > b + 10 && Math.abs(r - g) > 5);
      if (isSkin) {
        data[i] = 255; data[i+1] = 255; data[i+2] = 255; data[i+3] = 255;
      } else {
        data[i+3] = 0;
      }
    }
    binaryMaskCtx.putImageData(imgData, 0, 0);

    const featheredMaskCanvas = document.createElement('canvas');
    featheredMaskCanvas.width = cw; featheredMaskCanvas.height = ch;
    const featheredMaskCtx = featheredMaskCanvas.getContext('2d');
    featheredMaskCtx.filter = 'blur(12px)';
    featheredMaskCtx.drawImage(binaryMaskCanvas, 0, 0);
    featheredMaskCtx.filter = 'none';

    ctx.filter = 'brightness(1.05) contrast(1.02) saturate(1.1)';
    ctx.drawImage(rawCanvas, 0, 0);
    ctx.filter = 'none';

    featheredMaskCtx.globalCompositeOperation = 'source-in';
    featheredMaskCtx.drawImage(beautyCanvas, 0, 0);
    
    ctx.drawImage(featheredMaskCanvas, 0, 0);
    
    const url = canvasRef.value.toDataURL('image/jpeg', 0.98);
    localImages.value.push(url);
  }
};

const handleBack = () => {
  stopCamera();
  emit('back');
};

const stopCamera = () => {
  if (stream.value) {
    stream.value.getTracks().forEach(track => track.stop());
  }
};

onUnmounted(() => {
  window.removeEventListener('resize', checkOrientation);
  stopCamera();
});
</script>

<style scoped>
.capture-container {
  text-align: center;
  width: 100%;
}

.title {
  font-size: 2rem;
  color: #ff4d6d;
  margin-bottom: 20px;
}

.viewport-wrapper {
  position: relative;
  width: 100%;
  background: #2b2d42;
  border-radius: 24px;
  overflow: hidden;
  border: 6px solid #ffccd5;
  box-shadow: 0 12px 24px rgba(0, 0, 0, 0.08);
  margin: 0 auto;
  transition: all 0.3s ease;
}

.landscape-mode {
  aspect-ratio: 4/3;
  max-width: 100%;
}

.portrait-mode {
  aspect-ratio: 3/4;
  max-width: 340px;
}

.camera-stream {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transform: scaleX(-1);
  filter: brightness(1.35) contrast(0.85) saturate(1.2) blur(0.8px);
  transition: filter 0.1s ease;
}

.camera-stream.flash-effect {
  filter: brightness(2.5) contrast(0.6) blur(0px);
}

.countdown-display {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  background: rgba(255, 77, 109, 0.15);
}

.count-num {
  font-size: 7rem;
  font-weight: 800;
  color: #fff;
  text-shadow: 0 0 25px rgba(255, 77, 109, 0.9);
  animation: popEffect 1s infinite linear;
}

.indicator {
  position: absolute;
  bottom: 16px;
  right: 16px;
  background: rgba(89, 13, 34, 0.85);
  color: #fff;
  padding: 6px 18px;
  border-radius: 30px;
  font-size: 0.9rem;
  font-weight: 600;
}

.actions {
  margin-top: 24px;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 12px;
  position: relative;
}

.btn-capture {
  font-family: inherit;
  font-size: 1.2rem;
  font-weight: 600;
  background: #ff4d6d;
  color: #fff;
  border: none;
  padding: 14px 36px;
  border-radius: 50px;
  cursor: pointer;
  box-shadow: 0 6px 12px rgba(255, 77, 109, 0.25);
  transition: all 0.2s;
}

.btn-capture:hover {
  transform: translateY(-2px);
  background: #ff3355;
}

.btn-back {
  font-family: inherit;
  background: none;
  border: 2px solid #ff758f;
  color: #ff758f;
  padding: 8px 22px;
  border-radius: 50px;
  cursor: pointer;
  font-weight: 600;
}

.capturing-text {
  font-size: 1.2rem;
  color: #ff4d6d;
  font-weight: 600;
}

@keyframes popEffect {
  0% { transform: scale(0.6); opacity: 0; }
  15% { opacity: 1; }
  85% { transform: scale(1.15); opacity: 0.8; }
  100% { transform: scale(1.25); opacity: 0; }
}
</style>
