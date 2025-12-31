<template>
  <!-- Toggle-Button für Filter -->
  <button @click="showFilters = !showFilters">
    {{ showFilters ? 'Hide Filters' : 'Show Filters' }}
  </button>

  <!-- Filter-Komponente -->
  <GalleryFilter
    v-if="showFilters"
    :reverse-order="reverseOrder"
    @toggle-order="toggleOrder"
    :selected-size="selectedSize"
    @update-size="updateSize"
    :search-query="searchQuery"
    @update-search="updateSearch"
    @toggle-mock="toggleMockData"
    :use-mock-data="useMockData"
  />

  <!-- Galerie -->
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
        <img :src="image.path" :alt="image.description" @click="openImage(image)"/>
      </div>
    </div>
  </div>

  <!-- Lightbox -->
<Lightbox
  v-if="openLightbox"
  :images="flatImages.map(img => img.path)"
  :currentIndex="currentIndex"
  @close="openLightbox = false"
  @update:index="currentIndex = $event"
/>
</template>

<script setup>
import { ref, computed, onMounted, onBeforeUnmount } from 'vue'
import pictureData from "../assets/data/pictureData.json"
import mockPictureData from "../assets/data/mockPictureData.json"
import GalleryFilter from "./GalleryFilter.vue"
import Lightbox from "./Lightbox.vue"

const columns = ref([])
const columnCount = ref(1)
const reverseOrder = ref(true)
const showFilters = ref(false)
const useMockData = ref(false) // 👈 Mockdaten-Schalter

const selectedSize = ref('')
const searchQuery = ref('')

// --- LIGHTBOX STATE ---
const openLightbox = ref(false)
const currentIndex = ref(0)
const flatImages = computed(() => filteredImages.value)

// --- FILTERED IMAGES ---
const filteredImages = computed(() => {
  // 👇 Dynamisch zwischen echten und Mockdaten wechseln
  let images = useMockData.value ? [...mockPictureData] : [...pictureData]

  // Reihenfolge
  if (reverseOrder.value) {
    images = images.reverse()
  }

  // Size-Filter
  if (selectedSize.value) {
    images = images.filter(img => img.size === selectedSize.value)
  }

  // Suche (in name + description)
  if (searchQuery.value.trim()) {
    const q = searchQuery.value.toLowerCase()
    images = images.filter(
      img =>
        img.name?.toLowerCase().includes(q) ||
        img.description?.toLowerCase().includes(q)
    )
  }

  return images
})

// --- METHODEN ---
function toggleOrder() {
  reverseOrder.value = !reverseOrder.value
  distributeImages()
}

function updateSize(size) {
  selectedSize.value = size
  distributeImages()
}

function updateSearch(query) {
  searchQuery.value = query
  distributeImages()
}

// 👇 Umschalten zwischen Mock- und echten Daten
function toggleMockData() {
  useMockData.value = !useMockData.value
  // Optional: Filter zurücksetzen
  selectedSize.value = ''
  searchQuery.value = ''
  distributeImages()
}

function distributeImages() {
  const cols = Array.from({ length: columnCount.value }, () => [])
  const images = filteredImages.value
  images.forEach((img, index) => {
    cols[index % columnCount.value].push(img)
  })
  columns.value = cols
}

// Dynamische Spaltenanzahl
function calculateColumnCount() {
  const width = window.innerWidth

  if (width >= 1280) columnCount.value = 5
  else if (width >= 1024) columnCount.value = 4
  else if (width >= 768) columnCount.value = 3
  else if (width >= 600) columnCount.value = 2
  /* else columnCount.value = 1 */

  distributeImages()
}

// --- LIGHTBOX ---
function openImage(image) {
  const index = flatImages.value.findIndex(
    img => img.path === image.path
  )
  if (index !== -1) {
    currentIndex.value = index
    openLightbox.value = true
  }
}

onMounted(() => {
  calculateColumnCount()
  distributeImages()
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
