<template>
  <teleport to="body">
    <div v-if="isOpen" class="popup-overlay" @click.self="$emit('close')">
      <div class="popup-content">
        <slot></slot>
      </div>
    </div>
  </teleport>
</template>

<script setup>
defineProps({
  isOpen: {
    type: Boolean,
    required: true
  }
});
defineEmits(['close']);
</script>

<style scoped>
.popup-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: rgba(0, 0, 0, 0.4);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 9999;
}
.popup-content {
  background: #ffffff;
  padding: 30px;
  border-radius: 12px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.15);
  max-width: 420px;
  width: 90%;
  box-sizing: border-box;
}
</style>
