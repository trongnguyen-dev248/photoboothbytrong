<template>
  <div class="upload-container">
    <h2 class="title">Thêm Ảnh Từ Máy</h2>
    <p class="subtitle">Vui lòng chọn đủ <b>{{ expectedCount }}</b> tấm ảnh nhé ✨</p>

    <div class="upload-grid" :class="expectedCount === 3 ? 'grid-3' : 'grid-4'">
      <div v-for="i in expectedCount" :key="i" class="upload-slot" @click="triggerInput(i-1)">
        <img v-if="previewImages[i-1]" :src="previewImages[i-1]" class="preview-img" />
        <div v-else class="placeholder">
          <span class="plus">+</span>
          <span class="slot-label">Ảnh {{ i }}</span>
        </div>
      </div>
    </div>

    <input 
      type="file" 
      ref="fileInput" 
      style="display: none" 
      accept="image/*" 
      multiple 
      @change="handleFileChange" 
    />

    <div class="actions">
      <button class="btn-complete" :disabled="!isReady" @click="completeUpload">Tiếp theo ➡️</button>
      <button class="btn-back" @click="$emit('back')">Quay lại</button>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';

const props = defineProps({
  expectedCount: Number
});
const emit = defineEmits(['complete', 'back']);

const fileInput = ref(null);
const previewImages = ref(new Array(props.expectedCount).fill(null));
const activeSlot = ref(null);

const isReady = computed(() => {
  return previewImages.value.every(img => img !== null);
});

const triggerInput = (index) => {
  activeSlot.value = index;
  fileInput.value.click();
};

const handleFileChange = (e) => {
  const files = Array.from(e.target.files);
  if (!files.length) return;

  files.forEach((file, index) => {
    const targetIdx = activeSlot.value + index;
    if (targetIdx < props.expectedCount) {
      const reader = new FileReader();
      reader.onload = (event) => {
        previewImages.value[targetIdx] = event.target.result;
      };
      reader.readAsDataURL(file);
    }
  });
  e.target.value = '';
};

const completeUpload = () => {
  emit('complete', previewImages.value);
};
</script>

<style scoped>
.upload-container { text-align: center; width: 100%; }
.title { font-size: 2rem; color: #ff4d6d; margin-bottom: 10px; }
.subtitle { color: #590d22; margin-bottom: 25px; }
.upload-grid { display: grid; gap: 15px; margin-bottom: 30px; justify-content: center; }
.grid-4 { grid-template-columns: repeat(2, 1fr); }
.grid-3 { grid-template-columns: repeat(3, 1fr); }
.upload-slot { aspect-ratio: 3/4; background: #fff; border: 3px dashed #ffccd5; border-radius: 16px; display: flex; justify-content: center; align-items: center; cursor: pointer; overflow: hidden; transition: all 0.2s; }
.upload-slot:hover { border-color: #ff4d6d; background: #fff0f3; transform: scale(1.02); }
.preview-img { width: 100%; height: 100%; object-fit: cover; }
.placeholder { display: flex; flex-direction: column; align-items: center; color: #ff758f; }
.plus { font-size: 2rem; font-weight: bold; }
.slot-label { font-size: 0.8rem; font-weight: 600; }
.actions { display: flex; flex-direction: column; align-items: center; gap: 12px; }
.btn-complete { font-family: inherit; font-size: 1.2rem; font-weight: 700; background: #ff4d6d; color: #fff; border: none; padding: 14px 45px; border-radius: 50px; cursor: pointer; transition: all 0.2s; }
.btn-complete:disabled { opacity: 0.5; cursor: not-allowed; transform: none; }
.btn-back { font-family: inherit; background: none; border: 2px solid #ff758f; color: #ff758f; padding: 8px 25px; border-radius: 50px; cursor: pointer; font-weight: 600; }
@media (max-width: 480px) { .upload-grid { gap: 10px; } .upload-slot { border-radius: 12px; } }
</style>
