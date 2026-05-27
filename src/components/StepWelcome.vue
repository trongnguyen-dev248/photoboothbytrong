<template>
  <div class="welcome-container">
    <div class="title-block">
      <span class="title-top">PHOTOBOOTH</span>
      <span class="title-bottom">BY TRỌNG</span>
    </div>
    
    <div class="graphic-element">📸</div>
    
    <div class="main-actions">
      <button class="btn-start" @click="$emit('next', 'capture')">Chụp ảnh ngay ✨</button>
      <button class="btn-upload-mode" @click="$emit('next', 'upload')">Tạo ảnh từ máy 📂</button>
    </div>

    <div class="device-notice">
      <span class="notice-icon">💻</span>
      <p class="notice-text">Mẹo nhỏ: Hãy sử dụng laptop hoặc máy tính để có trải nghiệm chụp hình "cháy" nhất nhé!</p>
    </div>

    <div class="feedback-wrapper">
      <div v-if="!showContact" class="feedback-link" @click="showContact = true">
        Góp ý ý tưởng? Liên hệ ngay ✨
      </div>
      <div v-else class="feedback-box" @click="showContact = false">
        <span class="phone-number">0982413353</span>
        <span class="zalo-note">(Nhắn Zalo nhé 💬)</span>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
defineEmits(['next']);
const showContact = ref(false);
</script>

<style scoped>
.welcome-container {
  text-align: center;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  gap: 25px;
  width: 100%;
}

.title-block {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 6px;
  animation: bounce 2.5s ease-in-out infinite;
}

.title-top, .title-bottom {
  font-size: clamp(1.8rem, 5.5vw, 2.6rem);
  font-weight: 900;
  color: #ff4d6d;
  text-transform: uppercase;
  letter-spacing: -0.5px;
  line-height: 1.1;
  text-shadow:
    2px 2px 0 #fff,
    -2px 2px 0 #fff,
    -2px -2px 0 #fff,
    2px -2px 0 #fff,
    4px 4px 0px #ffb3c1;
}

.title-bottom {
  font-size: clamp(1.6rem, 5vw, 2.3rem);
  color: #ff758f;
}

.graphic-element {
  font-size: clamp(3.8rem, 11vw, 4.8rem);
  filter: drop-shadow(0 8px 12px rgba(255, 77, 109, 0.2));
  animation: pulse 1.5s infinite alternate ease-in-out;
  margin: -5px 0;
}

.main-actions {
  display: flex;
  flex-direction: column;
  gap: 12px;
  width: 100%;
  align-items: center;
}

.btn-start {
  font-family: inherit;
  font-size: 1.2rem;
  font-weight: 700;
  color: #fff;
  background: linear-gradient(135deg, #ff758f, #ff4d6d);
  border: none;
  padding: 15px 0;
  border-radius: 50px;
  cursor: pointer;
  box-shadow: 0 6px 20px rgba(255, 77, 109, 0.3);
  transition: all 0.2s ease;
  width: 270px;
}

.btn-upload-mode {
  font-family: inherit;
  font-size: 1.05rem;
  font-weight: 600;
  color: #ff4d6d;
  background: #fff;
  border: 2.5px solid #ff758f;
  padding: 12px 0;
  border-radius: 50px;
  cursor: pointer;
  transition: all 0.2s ease;
  width: 270px;
}

.btn-start:hover, .btn-upload-mode:hover {
  transform: translateY(-3px);
  box-shadow: 0 8px 16px rgba(255, 77, 109, 0.25);
}

.btn-start:active, .btn-upload-mode:active {
  transform: translateY(-1px);
}

.device-notice {
  display: flex;
  align-items: center;
  gap: 12px;
  background-color: #fff5f5;
  border: 2px dashed #ffb3c1;
  padding: 12px 18px;
  border-radius: 16px;
  max-width: 420px;
  width: 90%;
  box-shadow: 0 4px 10px rgba(255, 179, 193, 0.1);
}

.notice-icon {
  font-size: 1.3rem;
  animation: wiggle 2s infinite;
}

.notice-text {
  font-size: 0.85rem;
  color: #ff4d6d;
  font-weight: 600;
  line-height: 1.4;
  text-align: left;
}

.feedback-wrapper {
  min-height: 36px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.feedback-link {
  font-size: 0.85rem;
  color: #ff758f;
  cursor: pointer;
  font-weight: 500;
  text-decoration: underline;
  transition: color 0.2s;
}

.feedback-link:hover {
  color: #ff4d6d;
}

.feedback-box {
  display: inline-flex;
  align-items: center;
  gap: 6px;
  background: #fff0f3;
  border: 1.5px dashed #ffccd5;
  padding: 6px 14px;
  border-radius: 20px;
  cursor: pointer;
  animation: fadeIn 0.3s ease;
}

.phone-number {
  font-size: 0.9rem;
  font-weight: 700;
  color: #ff4d6d;
  letter-spacing: 0.5px;
}

.zalo-note {
  font-size: 0.8rem;
  color: #590d22;
  font-weight: 500;
}

@keyframes bounce {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-6px); }
}

@keyframes pulse {
  0% { transform: scale(1) rotate(-2deg); }
  100% { transform: scale(1.04) rotate(2deg); }
}

@keyframes wiggle {
  0%, 100% { transform: rotate(0); }
  25% { transform: rotate(-6deg); }
  75% { transform: rotate(6deg); }
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(4px); }
  to { opacity: 1; transform: translateY(0); }
}
</style>
