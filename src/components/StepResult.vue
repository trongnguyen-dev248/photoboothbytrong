<template>
  <div class="result-container">
    <transition name="toast-fade">
      <div v-if="showToast" class="toast-alert">
        Tải dải ảnh thành công! 🎉
      </div>
    </transition>

    <h2 class="title">Hoàn Thành! 🎉</h2>

    <div class="strip-container">
      <div 
        ref="stripRef" 
        class="photo-strip" 
        :class="[layoutType, { 'is-portrait-strip': isPortraitImages }]" 
        :style="frame.type === 'color' ? { backgroundColor: frame.value } : { backgroundImage: `url(${frame.value})` }"
      >
        <div v-if="frame.type === 'color'" class="strip-header">PHOTOBOOTH BY TRỌNG</div>
        
        <div class="strip-grid">
          <div v-for="(img, idx) in images" :key="idx" class="strip-item">
            <img :src="img" class="strip-img" />
          </div>
        </div>
        
        <div v-if="frame.type === 'color'" class="strip-footer">♥ {{ formattedDate }} ♥</div>
        <div v-else class="strip-footer-tiny-portrait">{{ formattedDate }}</div>
      </div>
    </div>

    <div class="action-panel">
      <button class="btn-download" @click="exportStrip">Tải dải ảnh về máy 💾</button>
      <button class="btn-reset" @click="$emit('reset')">Chụp lại từ đầu</button>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';
import html2canvas from 'html2canvas';

const props = defineProps({
  images: {
    type: Array,
    required: true
  },
  frame: {
    type: Object,
    required: true
  },
  layoutType: {
    type: String,
    required: true
  }
});

defineEmits(['reset']);

const stripRef = ref(null);
const isPortraitImages = ref(false);
const showToast = ref(false);

const formattedDate = computed(() => {
  const now = new Date();
  const dd = String(now.getDate()).padStart(2, '0');
  const mm = String(now.getMonth() + 1).padStart(2, '0');
  const yyyy = now.getFullYear();
  return `${dd}.${mm}.${yyyy}`;
});

const detectOrientation = () => {
  if (props.images.length > 0) {
    const img = new Image();
    img.src = props.images[0];
    img.onload = () => {
      isPortraitImages.value = img.height > img.width;
    };
  }
};

onMounted(() => {
  detectOrientation();
});

const triggerToast = () => {
  showToast.value = true;
  setTimeout(() => {
    showToast.value = false;
  }, 2500);
};

const exportStrip = async () => {
  if (!stripRef.value) return;
  try {
    const currentScrollX = window.scrollX;
    const currentScrollY = window.scrollY;

    window.scrollTo(0, 0);
    await new Promise((resolve) => setTimeout(resolve, 50));

    const canvas = await html2canvas(stripRef.value, {
      useCORS: true,
      scale: 4, 
      backgroundColor: null,
      logging: false,
      imageTimeout: 0,
      scrollX: 0,
      scrollY: 0,
      width: stripRef.value.offsetWidth,
      height: stripRef.value.offsetHeight
    });
    
    window.scrollTo(currentScrollX, currentScrollY);

    const link = document.createElement('a');
    link.download = `photobooth-by-trong-${Date.now()}.png`;
    link.href = canvas.toDataURL('image/png', 1.0);
    link.click();
    triggerToast();
  } catch (err) {
    alert('Có lỗi xảy ra khi xuất ảnh.');
  }
};
</script>

<style scoped>
.result-container {
  text-align: center;
  width: 100%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  position: relative;
}

.toast-alert {
  position: fixed;
  top: 30px;
  left: 50%;
  transform: translateX(-50%);
  background: linear-gradient(135deg, #ff758f, #ff4d6d);
  color: white;
  padding: 14px 32px;
  border-radius: 50px;
  font-weight: 700;
  font-size: 1.05rem;
  box-shadow: 0 10px 25px rgba(255, 77, 109, 0.4);
  border: 3px solid #fff;
  z-index: 9999;
}

.toast-fade-enter-active {
  animation: slideIn 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
}
.toast-fade-leave-active {
  animation: slideIn 0.2s reverse ease-in;
}

@keyframes slideIn {
  from { top: -60px; opacity: 0; transform: translateX(-50%) scale(0.9); }
  to { top: 30px; opacity: 1; transform: translateX(-50%) scale(1); }
}

.title {
  font-size: 2rem;
  color: #ff4d6d;
  margin-bottom: 20px;
}

.strip-container {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  margin-bottom: 24px;
  padding: 0 10px;
}

.photo-strip {
  padding: 60px 24px 45px 24px;
  box-shadow: 0 15px 35px rgba(89, 13, 34, 0.15);
  border-radius: 12px;
  display: flex;
  flex-direction: column;
  align-items: center;
  background-size: 100% 100%;
  background-repeat: no-repeat;
  background-position: center;
  box-sizing: border-box;
  position: relative;
  width: 100%;
}

.strip-header {
  font-size: 1rem;
  font-weight: 800;
  color: #fff;
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.6), 0 0 10px rgba(0, 0, 0, 0.4);
  margin-bottom: 20px;
  letter-spacing: 2px;
}

.strip-grid {
  display: flex;
  gap: 14px;
  width: 100%;
}

.strip-item {
  overflow: hidden;
  border: 4px solid #fff;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.12);
  background: #fdfdfd;
  box-sizing: border-box;
}

.strip-img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  display: block;
}

.strip-footer {
  margin-top: 20px;
  font-size: 0.85rem;
  font-weight: 700;
  color: #fff;
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.6), 0 0 10px rgba(0, 0, 0, 0.4);
}

.strip-footer-tiny-portrait {
  position: absolute;
  bottom: 12px;
  right: 16px;
  font-size: 0.7rem;
  font-weight: 600;
  color: #fff;
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.8), 0 0 4px rgba(0, 0, 0, 0.5);
}

.vertical {
  max-width: 290px;
}
.vertical .strip-grid {
  flex-direction: column;
}
.vertical .strip-item {
  width: 100%;
  aspect-ratio: 4/3;
}

.grid2x2 {
  max-width: 390px;
  padding: 55px 24px 40px 24px;
}
.grid2x2 .strip-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
}
.grid2x2 .strip-item {
  width: 100%;
  aspect-ratio: 4/3;
}

.wide3 {
  max-width: 410px;
}
.wide3 .strip-grid {
  flex-direction: column;
}
.wide3 .strip-item {
  width: 100%;
  aspect-ratio: 16/9;
}

.is-portrait-strip.vertical .strip-item {
  aspect-ratio: 3/4;
}

.is-portrait-strip.grid2x2 .strip-item {
  aspect-ratio: 3/4;
}

.is-portrait-strip.wide3 .strip-item {
  aspect-ratio: 4/5;
}

.action-panel {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 14px;
  width: 100%;
  margin-top: 15px;
}

.btn-download {
  font-family: inherit;
  font-size: 1.25rem;
  font-weight: 700;
  background: linear-gradient(135deg, #ff758f, #ff4d6d);
  color: #fff;
  border: none;
  padding: 14px 50px;
  border-radius: 50px;
  cursor: pointer;
  box-shadow: 0 6px 18px rgba(255, 77, 109, 0.35);
  transition: transform 0.2s, box-shadow 0.2s;
}

.btn-download:hover {
  transform: translateY(-2px);
  box-shadow: 0 10px 22px rgba(255, 77, 109, 0.45);
}

.btn-reset {
  font-family: inherit;
  background: none;
  border: 2px solid #ff758f;
  color: #ff758f;
  padding: 10px 28px;
  border-radius: 50px;
  cursor: pointer;
  font-weight: 600;
  transition: all 0.2s;
}

.btn-reset:hover {
  background: #ff758f;
  color: #fff;
}

@media (max-width: 480px) {
  .vertical { max-width: 240px; }
  .grid2x2 { max-width: 310px; padding: 45px 18px 35px 18px; }
  .wide3 { max-width: 310px; }
  .photo-strip { padding: 45px 16px 35px 16px; }
  .strip-grid { gap: 10px; }
  .strip-item { border: 3px solid #fff; }
  .strip-footer-tiny-portrait { bottom: 10px; right: 12px; font-size: 0.6rem; }
  .btn-download { padding: 12px 40px; font-size: 1.15rem; }
}
</style>
