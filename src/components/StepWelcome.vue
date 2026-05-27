<template>
  <div class="welcome-container">
    <h1 class="title">PHOTOBOOTH BY TRỌNG</h1>
    <div class="graphic-element">📸</div>
    <button class="btn-start" @click="$emit('next')">Bắt đầu ngay ✨</button>

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
  gap: 28px;
  width: 100%;
}

.title {
  font-size: clamp(1.8rem, 5.5vw, 2.6rem);
  font-weight: 900;
  color: #ff4d6d;
  text-transform: uppercase;
  letter-spacing: -0.5px;
  line-height: 1.2;
  word-break: break-word;
  text-shadow:
    2px 2px 0 #fff,
    -2px 2px 0 #fff,
    -2px -2px 0 #fff,
    2px -2px 0 #fff,
    4px 4px 0px #ffb3c1;
  animation: bounce 2.5s ease-in-out infinite;
  padding: 0 10px;
}

.graphic-element {
  font-size: clamp(4rem, 12vw, 5.5rem);
  filter: drop-shadow(0 10px 15px rgba(255, 77, 109, 0.25));
  animation: pulse 1.5s infinite alternate ease-in-out;
}

.btn-start {
  font-family: inherit;
  font-size: clamp(1.1rem, 3.5vw, 1.25rem);
  font-weight: 700;
  color: #fff;
  background: linear-gradient(135deg, #ff758f, #ff4d6d);
  border: none;
  padding: 16px 48px;
  border-radius: 50px;
  cursor: pointer;
  box-shadow: 0 8px 25px rgba(255, 77, 109, 0.35);
  transition: all 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
}

.btn-start:hover {
  transform: translateY(-4px) scale(1.02);
  box-shadow: 0 14px 28px rgba(255, 77, 109, 0.45);
}

.btn-start:active {
  transform: translateY(-1px) scale(0.98);
}

.device-notice {
  display: flex;
  align-items: center;
  gap: 10px;
  background-color: #fff5f5;
  border: 2px dashed #ffb3c1;
  padding: 10px 20px;
  border-radius: 16px;
  max-width: 420px;
  width: 90%;
  box-shadow: 0 4px 10px rgba(255, 179, 193, 0.15);
}

.notice-icon {
  font-size: 1.4rem;
  animation: wiggle 2s infinite;
}

.notice-text {
  font-size: 0.88rem;
  color: #ff4d6d;
  font-weight: 600;
  line-height: 1.4;
  text-align: left;
}

.feedback-wrapper {
  min-height: 38px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.feedback-link {
  font-size: 0.88rem;
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
  gap: 8px;
  background: #fff0f3;
  border: 1.5px dashed #ffccd5;
  padding: 6px 16px;
  border-radius: 20px;
  cursor: pointer;
  animation: fadeIn 0.3s ease;
}

.phone-number {
  font-size: 0.95rem;
  font-weight: 700;
  color: #ff4d6d;
  letter-spacing: 0.5px;
}

.zalo-note {
  font-size: 0.85rem;
  color: #590d22;
  font-weight: 500;
}

@keyframes bounce {
  0%, 100% { transform: translateY(0); }
  50% { transform: translateY(-8px); }
}

@keyframes pulse {
  0% { transform: scale(1) rotate(-3deg); }
  100% { transform: scale(1.06) rotate(3deg); }
}

@keyframes wiggle {
  0%, 100% { transform: rotate(0); }
  25% { transform: rotate(-8deg); }
  75% { transform: rotate(8deg); }
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(4px); }
  to { opacity: 1; transform: translateY(0); }
}
</style>
