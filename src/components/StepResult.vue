<template>
  <div class="result-container">
    <h2 class="title">Hoàn Thành! 🎉</h2>

    <div class="strip-container">
      <div 
        ref="stripRef" 
        class="photo-strip" 
        :class="[layoutType, { 'is-portrait-strip': isPortraitImages, 'is-image-theme': frame.type === 'image' }]" 
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
      <button class="btn-download" @click="exportStrip">Tải dải ảnh về máy</button>
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
      // Nếu chiều cao > chiều rộng -> Ảnh chụp dọc
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
    // Tính toán kích thước thật của element để export chính xác, không bị cắt
    const currentWidth = stripRef.value.offsetWidth;
    const currentHeight = stripRef.value.offsetHeight;

    const canvas = await html2canvas(stripRef.value, {
      useCORS: true,
      scale: 3, // Tăng chất lượng ảnh export
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
}

.title {
  font-size: 2rem;
  color: #ff4d6d;
  margin-bottom: 24px;
}

.strip-container {
  display: flex;
  justify-content: center;
  margin-bottom: 28px;
  overflow: visible; /* Đảm bảo shadow không bị cắt */
}

/* Base style cho dải ảnh */
.photo-strip {
  padding: 60px 20px 45px 20px;
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
  transition: all 0.3s ease; /* Animation mượt mà */
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
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.15);
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

/* --- Responsive Layout cho ảnh dọc (Điện thoại) --- */
.is-portrait-strip {
  /* Điều chỉnh padding cho khung đứng */
  padding: 20px 20px 50px 20px; 
}

.is-portrait-strip .strip-item {
  /* Tỷ lệ ô ảnh chụp dọc thon hơn */
  aspect-ratio: 3/4 !important; 
}

.strip-footer-tiny-portrait {
  position: absolute;
  bottom: 10px;
  right: 14px;
  font-size: 0.68rem;
  font-weight: 600;
  color: #fff;
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.8), 0 0 4px rgba(0, 0, 0, 0.5);
}

/* --- Định nghĩa kích thước dải ảnh gốc --- */
.vertical {
  width: 280px;
}
.vertical .strip-grid {
  flex-direction: column;
}
.vertical .strip-item {
  width: 100%;
  aspect-ratio: 4/3; /* Mặc định ngang */
}

.grid2x2 {
  width: 380px;
  padding: 55px 24px 40px 24px;
}
.grid2x2 .strip-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
}
.grid2x2 .strip-item {
  width: 100%;
  aspect-ratio: 4/3; /* Mặc định ngang */
}

.wide3 {
  width: 400px;
}
.wide3 .strip-grid {
  flex-direction: column;
}
.wide3 .strip-item {
  width: 100%;
  aspect-ratio: 16/9; /* Mặc định ngang */
}

/* --- Thêm Responsive Style cho Mobile --- */
@media (max-width: 480px) {
  .vertical { width: 220px; }
  .grid2x2 { width: 290px; }
  .wide3 { width: 290px; }
  
  /* Điều chỉnh padding khi dọc trên mobile */
  .vertical.is-portrait-strip { padding: 15px 15px 40px 15px; }
  
  .strip-grid { gap: 10px; }
  .strip-item { border: 3px solid #fff; }
  
  /* Vị trí ngày tháng cho ảnh dọc mobile */
  .strip-footer-tiny-portrait { bottom: 8px; right: 10px; font-size: 0.6rem; }
}
</style>
