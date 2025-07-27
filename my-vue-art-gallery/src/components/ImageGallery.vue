<template>

   <button @click="toggleOrder">
  Reihenfolge {{ reverseOrder ? 'umgekehrt' : 'normal' }}
</button>

  <div class="gallery-grid">
    <div
      v-for="(column, colIndex) in columns"
      :key="colIndex"
      class="gallery-column"
    >
      <div
        v-for="(image, index) in column"
        :key="index"
        class="gallery-item"
      >
        <img :src="image.path" :alt="image.description" />
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onBeforeUnmount } from 'vue'
import pictureData from "../assets/data/pictureData.json"

const columns = ref([])
const columnCount = ref(1)
const reverseOrder = ref(false)

function toggleOrder() { //für buttonsteuerung der bildreihenfolge umdrehen
  reverseOrder.value = !reverseOrder.value
  distributeImages()
}

function distributeImages() {
  // Init leere Arrays je Spalte
  const cols = Array.from({ length: columnCount.value }, () => [])

  const images = reverseOrder.value //hier reihenfolge umdreh funktion
  ? [...pictureData].reverse()
  : pictureData
  
  images.forEach((img, index) => {
    // Verteile horizontal: jedes Bild kommt in (index % spaltenanzahl)
    cols[index % columnCount.value].push(img)
  })

  columns.value = cols
}

// Dynamisch Anzahl Spalten ermitteln basierend auf Fensterbreite
function calculateColumnCount() {
  const width = window.innerWidth

  if (width >= 1280) columnCount.value = 5
  else if (width >= 1024) columnCount.value = 4
  else if (width >= 768) columnCount.value = 3
  else if (width >= 600) columnCount.value = 2
  else columnCount.value = 1

  distributeImages()
}

onMounted(() => {
  calculateColumnCount()
  window.addEventListener('resize', calculateColumnCount)
})

onBeforeUnmount(() => {
  window.removeEventListener('resize', calculateColumnCount)
})
</script>

<style scoped>
.gallery-grid {
  display: flex;
  gap: 8px;
  padding: 1rem;
}

.gallery-column {
  flex: 1;
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.gallery-item img {
  width: 100%;
  height: auto;
  display: block;
  border-radius: 6px;
  box-shadow: 0 1px 4px rgba(0, 0, 0, 0.1);
  transition: transform 0.2s ease;
}

.gallery-item img:hover {
  transform: scale(1.02);
}
</style>
