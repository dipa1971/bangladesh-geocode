<template>
  <div>
    <h2 class="section-title">📍 Districts of {{ division.name }} Division</h2>

    <div v-if="loading" class="loading-state">
      <span class="spinner"></span>
      <p>জেলার তথ্য লোড হচ্ছে...</p>
    </div>

    <div v-else-if="districts.length === 0" class="empty-state">
      <p>কোনো জেলার তথ্য পাওয়া যায়নি।</p>
    </div>

    <div v-else class="cards-grid">
      <div v-for="district in districts" :key="district.id" class="card">
        <h3>{{ district.name }}</h3>
        <p class="bengali-text">Bengali: {{ district.bn_name }}</p>
        <p class="website-link" v-if="district.url">
          Website: <a :href="'https://' + district.url" target="_blank">{{ district.url }}</a>
        </p>
        <div class="card-buttons">
          <button class="btn-green" @click="$emit('select-district', district)">
            Proceed to see Upazilas
          </button>
          <button class="btn-blue" @click="$emit('view-details', district)">
            View Details
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const props = defineProps(['division'])
const districts = ref([])
const loading = ref(true)

const fetchDistricts = async () => {
  try {
    const res = await fetch('https://raw.githubusercontent.com/nuhil/bangladesh-geocode/master/districts/districts.json')
    const result = await res.json()
    districts.value = result[2].data.filter(d => String(d.division_id) === String(props.division.id))
  } catch (err) {
    console.error('Districts fetch error:', err)
  } finally {
    loading.value = false
  }
}

onMounted(() => {
  fetchDistricts()
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

.empty-state {
  text-align: center;
  padding: 50px 20px;
  color: #6b7280;
  font-size: 15px;
  background: #ffffff;
  border-radius: 16px;
  border: 1px dashed rgba(0, 90, 54, 0.2);
}
</style>