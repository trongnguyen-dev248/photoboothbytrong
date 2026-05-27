<template>
  <div class="type-container">
    <h2 class="section-title">Chọn Kiểu Dáng Ảnh</h2>
    <div class="grid-types">
      <div class="type-card" @click="selectType('vertical', 4)">
        <div class="preview-box vertical-preview">
          <div class="box"></div>
          <div class="box"></div>
          <div class="box"></div>
          <div class="box"></div>
        </div>
        <span class="type-name">4 Ảnh Dọc (Classic)</span>
      </div>

      <div class="type-card" @click="selectType('grid2x2', 4)">
        <div class="preview-box grid-preview">
          <div class="box"></div>
          <div class="box"></div>
          <div class="box"></div>
          <div class="box"></div>
        </div>
        <span class="type-name">Tứ Chiếu (Grid 2x2)</span>
      </div>

      <div class="type-card" @click="selectType('wide3', 3)">
        <div class="preview-box wide-preview">
          <div class="box"></div>
          <div class="box"></div>
          <div class="box"></div>
        </div>
        <span class="type-name">Phóng To (3 Ảnh Ngang)</span>
      </div>
    </div>
    <button class="btn-back" @click="$emit('back')">Quay lại</button>
  </div>
</template>

<script setup>
const emit = defineEmits(['select', 'back']);

const selectType = (layout, count) => {
  emit('select', { layout, count });
};
</script>

<style scoped>
.type-container {
  text-align: center;
  width: 100%;
}

.section-title {
  font-size: 2rem;
  color: #ff4d6d;
  margin-bottom: 28px;
}

.grid-types {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 16px;
  margin-bottom: 32px;
}

.type-card {
  background: #fff;
  border: 3px solid #ffccd5;
  border-radius: 20px;
  padding: 16px;
  cursor: pointer;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 12px;
  transition: all 0.2s ease;
}

.type-card:hover {
  transform: translateY(-4px);
  border-color: #ff4d6d;
  box-shadow: 0 8px 16px rgba(255, 77, 109, 0.15);
}

.preview-box {
  background: #fff0f3;
  padding: 8px;
  border-radius: 8px;
  display: flex;
  gap: 4px;
  width: 90px;
  height: 120px;
}

.box {
  background: #ffb3c1;
  border-radius: 2px;
}

.vertical-preview {
  flex-direction: column;
}
.vertical-preview .box {
  flex: 1;
}

.grid-preview {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  grid-template-rows: repeat(2, 1fr);
}

.wide-preview {
  flex-direction: column;
}
.wide-preview .box {
  flex: 1;
  aspect-ratio: 16/9;
}

.type-name {
  font-size: 0.95rem;
  font-weight: 600;
  color: #590d22;
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

@media (max-width: 560px) {
  .grid-types {
    grid-template-columns: 1fr;
  }
  .type-card {
    flex-direction: row;
    justify-content: flex-start;
    padding: 12px 20px;
  }
  .preview-box {
    width: 60px;
    height: 70px;
  }
}
</style>