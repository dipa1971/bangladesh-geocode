<template>
  <div>
    <h2 class="section-title">🇧🇩 Divisions of Bangladesh</h2>

    <div v-if="loading" class="loading-state">
      <span class="spinner"></span>
      <p>বিভাগের তথ্য লোড হচ্ছে...</p>
    </div>

    <div v-else class="cards-grid">
      <div v-for="item in divisions" :key="item.id" class="card">
        <h3>{{ item.name }}</h3>
        <p class="bengali-text">Bengali: {{ item.bn_name }}</p>
        <p class="website-link" v-if="item.url">
          Website: <a :href="'https://' + item.url" target="_blank">{{ item.url }}</a>
        </p>
        <div class="card-buttons">
          <button class="btn-green" @click="$emit('select-division', item)">
            Proceed to see Districts
          </button>
          <button class="btn-blue" @click="$emit('view-details', item)">
            View Details
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const divisions = ref([])
const loading = ref(true)

const fetchDivisions = async () => {
  try {
    const res = await fetch('https://raw.githubusercontent.com/nuhil/bangladesh-geocode/master/divisions/divisions.json')
    const result = await res.json()
    divisions.value = result[2].data
  } catch (err) {
    console.error('Divisions fetch error:', err)
  } finally {
    loading.value = false
  }
}

onMounted(() => {
  fetchDivisions()
})
</script>

<style scoped>
.loading-state {
  text-align: center;
  padding: 60px 20px;
  color: #006a4e;
}

.spinner {
  display: inline-block;
  width: 38px;
  height: 38px;
  border: 4px solid rgba(0, 106, 78, 0.15);
  border-top-color: #006a4e;
  border-radius: 50%;
  animation: spin 0.8s linear infinite;
  margin-bottom: 14px;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}

.loading-state p {
  font-size: 15px;
  font-weight: 600;
}
</style>