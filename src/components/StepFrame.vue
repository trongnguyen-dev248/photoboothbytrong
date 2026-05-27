<template>
  <div class="frame-container">
    <h2 class="title">Chọn Màu Khung Ảnh</h2>
    
    <div class="transition-wrapper">
      <transition name="page-zoom" mode="out-in">
        <div class="grid-frames" :key="currentPage">
          <div 
            v-for="item in paginatedFrames" 
            :key="item.id" 
            class="frame-card" 
            :style="item.type === 'color' ? { backgroundColor: item.value } : { backgroundImage: `url(${item.value})`, backgroundSize: 'cover', backgroundPosition: 'center' }"
            @click="$emit('select', item)"
          >
            <span class="frame-name">{{ item.name }}</span>
          </div>
        </div>
      </transition>
    </div>

    <div class="pagination-controls" v-if="totalPages > 1">
      <button class="btn-page" :disabled="currentPage === 1" @click="currentPage--">◀</button>
      <span class="page-info">{{ currentPage }} / {{ totalPages }}</span>
      <button class="btn-page" :disabled="currentPage === totalPages" @click="currentPage++">▶</button>
    </div>

    <button class="btn-back" @click="$emit('back')">Quay lại</button>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';

const props = defineProps({
  frames: {
    type: Array,
    required: true
  }
});

defineEmits(['select', 'back']);

const currentPage = ref(1);
const itemsPerPage = 4;

const totalPages = computed(() => Math.ceil(props.frames.length / itemsPerPage));

const paginatedFrames = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage;
  return props.frames.slice(start, start + itemsPerPage);
});
</script>

<style scoped>
.frame-container {
  text-align: center;
  width: 100%;
}

.title {
  font-size: 2rem;
  color: #ff4d6d;
  margin-bottom: 28px;
}

.transition-wrapper {
  position: relative;
  min-height: 240px;
  margin-bottom: 24px;
  width: 100%;
  overflow: hidden;
}

.grid-frames {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 20px;
  width: 100%;
}

.frame-card {
  height: 110px;
  border-radius: 20px;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
  border: 4px solid #fff;
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.04);
  transition: transform 0.2s ease, box-shadow 0.2s ease;
  background-color: #f3f4f6;
}

.frame-card:hover {
  transform: scale(1.03);
  box-shadow: 0 12px 20px rgba(255, 77, 109, 0.18);
}

.frame-name {
  font-weight: 600;
  color: #590d22;
  background: rgba(255, 255, 255, 0.85);
  padding: 6px 16px;
  border-radius: 12px;
  font-size: 1rem;
}

.pagination-controls {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 16px;
  margin-bottom: 28px;
}

.btn-page {
  background: #fff;
  border: 2px solid #ffccd5;
  color: #ff4d6d;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  font-size: 1.1rem;
  cursor: pointer;
  display: flex;
  justify-content: center;
  align-items: center;
  transition: all 0.2s;
}

.btn-page:hover:not(:disabled) {
  background: #ff4d6d;
  color: #fff;
  border-color: #ff4d6d;
}

.btn-page:disabled {
  opacity: 0.4;
  cursor: not-allowed;
}

.page-info {
  font-weight: 700;
  color: #590d22;
  font-size: 1.1rem;
}

.btn-back {
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

.btn-back:hover {
  background: #ff758f;
  color: #fff;
}

.page-zoom-enter-active,
.page-zoom-leave-active {
  transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
}

.page-zoom-enter-from {
  opacity: 0;
  transform: scale(0.9) translateX(20px);
}

.page-zoom-leave-to {
  opacity: 0;
  transform: scale(0.9) translateX(-20px);
}

@media (max-width: 480px) {
  .grid-frames {
    grid-template-columns: 1fr;
    gap: 14px;
  }
  .transition-wrapper {
    min-height: auto;
  }
}
</style>