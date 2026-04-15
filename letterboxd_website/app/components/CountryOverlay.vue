<template>
  <Transition name="fade">
    <div v-if="isVisible" class="overlay-backdrop" @click="close">
      <div class="overlay-modal" @click.stop>
        <div class="modal-header">
          <div class="title-container">
            <img v-if="flagUrl" :src="flagUrl" class="country-flag" />
            <h1 class="condensed-text">{{ countryProps?.ADMIN || 'Unknown' }}</h1>
          </div>
          <button class="close-button" @click="close" aria-label="Close">×</button>
        </div>
        <div class="modal-body">
          <!-- Future Letterboxd Dataviz goes here -->
          <p>Movie dataviz will be displayed here...</p>
        </div>
      </div>
    </div>
  </Transition>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  country: {
    type: Object,
    default: null
  },
  isVisible: {
    type: Boolean,
    default: false
  }
})

const emit = defineEmits(['close'])

const countryProps = computed(() => props.country?.properties)

const flagUrl = computed(() => {
  if (!countryProps.value) return null
  
  // Use ISO_A2 first, but if it's broken (-99), fallback to WB_A2 or FIPS_10_
  let isoCode = countryProps.value.ISO_A2
  if (!isoCode || isoCode === '-99') isoCode = countryProps.value.WB_A2
  if (!isoCode || isoCode === '-99') isoCode = countryProps.value.FIPS_10_
  if (!isoCode || isoCode === '-99') return null 

  return `https://flagcdn.com/${isoCode.toLowerCase()}.svg`
})

const close = () => {
  emit('close')
}
</script>

<style scoped>
.overlay-backdrop {
  position: absolute;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  z-index: 10;
  /* Darken the globe behind slightly so the modal pops out, and grab background clicks */
  background: rgba(0, 0, 0, 0.4);
  display: flex;
  align-items: center;
  justify-content: center;
}

.overlay-modal {
  width: 80%;
  height: 80%;
  background: rgba(255, 255, 255, 0.95);
  border-radius: 16px;
  box-shadow: 0 10px 50px rgba(0,0,0,0.3);
  display: flex;
  flex-direction: column;
  overflow: hidden;
  backdrop-filter: blur(10px);
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 25px 40px;
  border-bottom: 1px solid rgba(0,0,0,0.1);
}

.title-container {
  display: flex;
  align-items: center;
  gap: 20px;
}

.country-flag {
  height: 45px;
  border-radius: 4px;
  box-shadow: 0 2px 6px rgba(0,0,0,0.1);
  object-fit: cover;
}

.modal-header h1 {
  margin: 0;
  font-size: 3rem;
  color: #1a1a1a;
  line-height: 1;
}

.close-button {
  background: none;
  border: none;
  font-size: 3rem;
  line-height: 1;
  cursor: pointer;
  color: #888;
  transition: color 0.2s, transform 0.2s;
  padding: 0;
}

.close-button:hover {
  color: #333;
  transform: scale(1.1);
}

.modal-body {
  padding: 40px;
  flex: 1;
  overflow-y: auto;
  font-size: 1.2rem;
  color: #444;
}

/* Transition for smooth appearance/disappearance */
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.4s ease, transform 0.4s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
  transform: scale(0.95);
}
</style>
