<template>
  <div class="app-container">
    <main class="app-main">
      <StepWelcome v-if="currentStep === 1" @next="goToStep(2)" />
      <StepType v-if="currentStep === 2" @select="handleTypeSelect" @back="goToStep(1)" />
      <StepFrame v-if="currentStep === 3" :frames="frames" @select="handleFrameSelect" @back="goToStep(2)" />
      <StepCapture v-if="currentStep === 4" :expectedCount="selectedType.count" @complete="handleCaptureComplete" @back="goToStep(3)" />
      <StepResult v-if="currentStep === 5" :images="capturedImages" :frame="selectedFrame" :layoutType="selectedType.layout" @reset="resetApp" />
    </main>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import StepWelcome from './components/StepWelcome.vue';
import StepType from './components/StepType.vue';
import StepFrame from './components/StepFrame.vue';
import StepCapture from './components/StepCapture.vue';
import StepResult from './components/StepResult.vue';

const currentStep = ref(1);
const selectedType = ref({ layout: 'vertical', count: 4 });
const selectedFrame = ref({ id: 1, name: 'Hồng Baby', value: '#ffb6c1', type: 'color' });
const capturedImages = ref([]);

const frames = ref([
  { id: 1, name: 'Hồng Baby', value: '#ffb6c1', type: 'color' },
  { id: 2, name: 'Hồng Cherry', value: '#ff4d6d', type: 'color' },
  { id: 3, name: 'Hồng Thạch', value: '#fff0f5', type: 'color' },
  { id: 4, name: 'Hồng Y2K', value: '#ff85a2', type: 'color' },
  { id: 5, name: 'Hoa Anh Đào', value: '/themes/anhdao.png', type: 'image' },
  { id: 6, name: 'Hello Kitty', value: '/themes/kitty.png', type: 'image' },
  { id: 7, name: 'Ngôi Sao', value: '/themes/sao.png', type: 'image' },
  { id: 8, name: 'Polar Bear', value: '/themes/polabear.png', type: 'image' }
]);

const goToStep = (step) => {
  currentStep.value = step;
};

const handleTypeSelect = (typeData) => {
  selectedType.value = typeData;
  goToStep(3);
};

const handleFrameSelect = (frameObj) => {
  selectedFrame.value = frameObj;
  goToStep(4);
};

const handleCaptureComplete = (images) => {
  capturedImages.value = images;
  goToStep(5);
};

const resetApp = () => {
  capturedImages.value = [];
  selectedFrame.value = { id: 1, name: 'Hồng Baby', value: '#ffb6c1', type: 'color' };
  selectedType.value = { layout: 'vertical', count: 4 };
  currentStep.value = 1;
};
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Fredoka:wght@300..700&display=swap');

* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: 'Fredoka', sans-serif;
  background-color: #fff0f3;
  overflow-x: hidden;
}

.app-container {
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 20px;
}

.app-main {
  width: 100%;
  max-width: 650px;
  background: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(10px);
  border: 4px solid #ffb3c1;
  border-radius: 32px;
  padding: 40px;
  box-shadow: 0 20px 40px rgba(255, 77, 109, 0.15);
}

@media (max-width: 480px) {
  .app-main {
    padding: 24px;
    border-radius: 24px;
  }
}
</style>