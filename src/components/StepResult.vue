<template>
  <div class="result-container">
    <h2 class="title">Hoàn Thành! 🎉</h2>

    <div class="strip-container">
      <div 
        ref="stripRef" 
        class="photo-strip" 
        :class="[layoutType, { 'is-portrait-strip': isPortraitImages }]" 
        :style="frame.type === 'color' ? { backgroundColor: frame.value } : { backgroundImage: `url(${frame.value})` }"
      >
        <div v-if="frame.type === 'color' && !isPortraitImages" class="strip-header">PHOTOBOOTH BY TRỌNG</div>
        
        <div class="strip-grid">
          <div v-for="(img, idx) in images" :key="idx" class="strip-item">
            <img :src="img" class="strip-img" />
          </div>
        </div>
        
        <div v-if="frame.type === 'color' && !isPortraitImages" class="strip-footer">♥ {{ formattedDate }} ♥</div>
        <div v-if="isPortraitImages" class="strip-footer-tiny-portrait">{{ formattedDate }}</div>
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

const exportStrip = async () => {
  if (!stripRef.value) return;
  try {
    const currentWidth = stripRef.value.offsetWidth;
    const currentHeight = stripRef.value.offsetHeight;

    const canvas = await html2canvas(stripRef.value, {
      useCORS: true,
      scale: 3,
      backgroundColor: null,
      width: currentWidth,
      height: currentHeight,
      windowWidth: currentWidth,
      windowHeight: currentHeight
    });
    
    const link = document.createElement('a');
    link.download = `photobooth-by-trong-${Date.now()}.png`;
    link.href = canvas.toDataURL('image/png');
    link.click();
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
}

.title {
  font-size: 2rem;
  color: #ff4d6d;
  margin-bottom: 20px;
}

.strip-container {
  display: flex;
  justify-content: center;
  width: 100%;
  margin-bottom: 24px;
}

.photo-strip {
  padding: 55px 18px 40px 18px;
  box-shadow: 0 15px 35px rgba(89, 13, 34, 0.15);
  border-radius: 8px;
  display: flex;
  flex-direction: column;
  align-items: center;
  background-size: 100% 100%;
  background-repeat: no-repeat;
  background-position: center;
  box-sizing: border-box;
  position: relative;
}

.strip-header {
  font-size: 0.95rem;
  font-weight: 800;
  color: #fff;
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.6), 0 0 10px rgba(0, 0, 0, 0.4);
  margin-bottom: 16px;
  letter-spacing: 2px;
}

.strip-grid {
  display: flex;
  gap: 12px;
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
  margin-top: 16px;
  font-size: 0.8rem;
  font-weight: 700;
  color: #fff;
  text-shadow: 0 2px 4px rgba(0, 0, 0, 0.6), 0 0 10px rgba(0, 0, 0, 0.4);
}

.is-portrait-strip {
  width: 210px !important;
  padding: 40px 14px 35px 14px !important;
}

.is-portrait-strip .strip-item {
  aspect-ratio: 3/4 !important;
  border: 3px solid #fff !important;
}

.is-portrait-strip .strip-grid {
  gap: 8px !important;
}

.strip-footer-tiny-portrait {
  position: absolute;
  bottom: 12px;
  right: 14px;
  font-size: 0.65rem;
  font-weight: 600;
  color: #fff;
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.8), 0 0 4px rgba(0, 0, 0, 0.5);
}

.vertical {
  width: 270px;
}
.vertical .strip-grid {
  flex-direction: column;
}
.vertical .strip-item {
  width: 100%;
  aspect-ratio: 4/3;
}

.grid2x2 {
  width: 360px;
  padding: 50px 20px 35px 20px;
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
  width: 370px;
}
.wide3 .strip-grid {
  flex-direction: column;
}
.wide3 .strip-item {
  width: 100%;
  aspect-ratio: 16/9;
}

.action-panel {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 12px;
  width: 100%;
  margin-top: 10px;
}

.btn-download {
  font-family: inherit;
  font-size: 1.2rem;
  font-weight: 700;
  background: linear-gradient(135deg, #ff758f, #ff4d6d);
  color: #fff;
  border: none;
  padding: 14px 44px;
  border-radius: 50px;
  cursor: pointer;
  box-shadow: 0 6px 18px rgba(255, 77, 109, 0.3);
  transition: transform 0.2s, box-shadow 0.2s;
}

.btn-download:hover {
  transform: translateY(-2px);
  box-shadow: 0 10px 22px rgba(255, 77, 109, 0.4);
}

.btn-reset {
  font-family: inherit;
  background: none;
  border: 2px solid #ff758f;
  color: #ff758f;
  padding: 10px 26px;
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
  .vertical { width: 210px; }
  .grid2x2 { width: 270px; padding: 40px 16px 30px 16px; }
  .wide3 { width: 270px; }
  .is-portrait-strip { width: 175px !important; padding: 35px 12px 30px 12px !important; }
  .strip-grid { gap: 8px; }
  .strip-item { border: 2px solid #fff; }
  .strip-footer-tiny-portrait { bottom: 8px; right: 10px; font-size: 0.58rem; }
  .btn-download { padding: 12px 36px; font-size: 1.1rem; }
}
</style>
