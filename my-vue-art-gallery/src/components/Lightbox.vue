<template>
  <teleport to="body">
    <div class="lightbox" @click.self="close">
      <!-- Close-Button -->
      <button class="close" @click="close">×</button>

      <!-- Container für Pfeile + Bild -->
      <div class="lightbox-container">
        <button class="nav left" @click="prev">‹</button>
        <img :src="images[currentIndex]" class="image" />
        <button class="nav right" @click="next">›</button>
      </div>
    </div>
  </teleport>
</template>
<script setup>
import { onMounted, onUnmounted } from 'vue'

const props = defineProps({
  images: Array,
  currentIndex: Number
})

const emit = defineEmits(['close', 'update:index'])

const close = () => emit('close')

const next = () =>
  emit('update:index', (props.currentIndex + 1) % props.images.length)

const prev = () =>
  emit(
    'update:index',
    (props.currentIndex - 1 + props.images.length) % props.images.length
  )

const onKey = (e) => {
  if (e.key === 'Escape') close()
  if (e.key === 'ArrowRight') next()
  if (e.key === 'ArrowLeft') prev()
}

onMounted(() => window.addEventListener('keydown', onKey))
onUnmounted(() => window.removeEventListener('keydown', onKey))
</script>

<style scoped>
.lightbox {
  position: fixed;
  inset: 0;
  background: rgba(0,0,0,0.9);
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
  padding: 0 10px; /* minimaler Abstand zu den Rändern */
  box-sizing: border-box;
}

/* Close-Button oben rechts */
.close {
  position: absolute;
  top: 20px;
  right: 20px;
  font-size: 2rem;
  background: none;
  color: white;
  border: none;
  cursor: pointer;
}

/* Container für Pfeile + Bild */
.lightbox-container {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  max-width: 100%;
  gap: 10px; /* kleiner Abstand zwischen Pfeilen und Bild */
  box-sizing: border-box;
}

/* Bild */
.image {
  max-width: calc(100% - 100px); /* Platz für Pfeile links + rechts (50px pro Seite) */
  max-height: 80vh;
  display: block;
  border-radius: 6px;
  margin: 0 auto;
}

/* Pfeile */
.nav {
  font-size: 3rem;
  background: none; /* kein Hintergrund mehr */
  color: rgba(255,255,255,0.6); /* halbtransparent */
  border: none;
  cursor: pointer;
  padding: 0.5rem; /* kleine Klickfläche */
  transition: color 0.2s ease;
  user-select: none;
}

.nav:hover {
  color: rgba(255,255,255,0.9);
}

/* Pfeile links/rechts am Rand */
.left {
  margin-right: auto;
}
.right {
  margin-left: auto;
}
</style>
