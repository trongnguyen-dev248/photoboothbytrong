<template>
  <div class="capture-container">
    <h2 class="title">Phòng Chụp Ảnh</h2>
    
    <div class="viewport-wrapper">
      <video ref="videoRef" autoplay playsinline class="camera-stream" :class="{ 'flash-effect': isFlashing }"></video>
      <canvas ref="canvasRef" width="640" height="480" style="display: none;"></canvas>
      
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

onMounted(async () => {
  try {
    stream.value = await navigator.mediaDevices.getUserMedia({
      video: { width: 640, height: 480 }
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
    const cw = 640;
    const ch = 480;
    ctx.clearRect(0, 0, cw, ch);
    
    ctx.save();
    ctx.translate(cw, 0);
    ctx.scale(-1, 1);
    
    ctx.filter = 'brightness(1.15) contrast(0.9) saturate(1.35) blur(0.8px)';
    
    // Kỹ thuật Slim Face phần cứng: bóp nhẹ chiều ngang khoảng 6%
    const slimFactor = 0.94; 
    const drawW = cw * slimFactor;
    const drawX = (cw - drawW) / 2;
    
    ctx.drawImage(videoRef.value, drawX, 0, drawW, ch);
    
    ctx.restore();
    
    ctx.filter = 'none';
    
    const url = canvasRef.value.toDataURL('image/jpeg', 0.9);
    localImages.value.push(url);
  }
};

const stopCamera = () => {
  if (stream.value) {
    stream.value.getTracks().forEach(track => track.stop());
  }
};

const handleBack = () => {
  stopCamera();
  emit('back');
};

onUnmounted(() => {
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
  aspect-ratio: 4/3;
  background: #2b2d42;
  border-radius: 24px;
  overflow: hidden;
  border: 6px solid #ffccd5;
  box-shadow: 0 12px 24px rgba(0, 0, 0, 0.08);
}

.camera-stream {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transform: scaleX(-1);
  filter: brightness(1.15) contrast(0.9) saturate(1.35) blur(0.8px);
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