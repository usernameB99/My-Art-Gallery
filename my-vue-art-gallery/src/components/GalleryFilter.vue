<template>
  <div class="filter-box">
    <!-- Suchfeld -->
    <input
      type="text"
      placeholder="Suche..."
      v-model="localSearch"
      @input="$emit('update-search', localSearch)"
    />

    <!-- Reihenfolge Button -->
    <button @click="$emit('toggle-order')">
      Reihenfolge {{ reverseOrder ? 'umgekehrt' : 'normal' }}
    </button>

    <!-- Size Select -->
    <select v-model="localSize" @change="$emit('update-size', localSize)">
      <option value="">Alle Größen</option>
      <option
        v-for="size in uniqueSizes"
        :key="size"
        :value="size"
      >
        {{ size }}
      </option>
    </select>
  </div>
</template>

<script setup>
import { ref, watch, computed } from 'vue'
import pictureData from "../assets/data/pictureData.json"

// Props aus ImageGallery entgegennehmen
const props = defineProps({
  reverseOrder: Boolean,
  selectedSize: String,
  searchQuery: String
})

// Lokale States für Inputs
const localSearch = ref(props.searchQuery)
const localSize = ref(props.selectedSize)

// Unique Sizes aus den Bildern ziehen
const uniqueSizes = computed(() => {
  const sizes = pictureData.map(img => img.size)
  return [...new Set(sizes)]
})

// Props -> Local sync
watch(() => props.searchQuery, val => localSearch.value = val)
watch(() => props.selectedSize, val => localSize.value = val)
</script>

<style scoped>
.filter-box {
  margin: 1rem;
  padding: 1rem;
  border: 1px solid #ccc;
  border-radius: 8px;
  display: flex;
  gap: 1rem;
  align-items: center;
  justify-content: center;
  flex-wrap: wrap;
}
</style>
