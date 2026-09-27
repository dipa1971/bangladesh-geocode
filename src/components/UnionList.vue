<template>
  <div>
    <h2 class="section-title">🏡 Unions of {{ upazila.name }} Upazila</h2>

    <div v-if="loading" class="loading-state">
      <span class="spinner"></span>
      <p>ইউনিয়নের তথ্য লোড হচ্ছে...</p>
    </div>

    <div v-else-if="unions.length === 0" class="empty-state">
      <p>কোনো ইউনিয়নের তথ্য পাওয়া যায়নি।</p>
    </div>

    <div v-else class="cards-grid">
      <div v-for="union in unions" :key="union.id" class="card">
        <h3>{{ union.name }}</h3>
        <p class="bengali-text">Bengali: {{ union.bn_name }}</p>
        <p class="website-link" v-if="union.url">
          Website: <a :href="'https://' + union.url" target="_blank">{{ union.url }}</a>
        </p>
        <div class="card-buttons">
          <button class="btn-blue" @click="$emit('view-details', union)">
            View Details
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const props = defineProps(['upazila'])
const unions = ref([])
const loading = ref(true)

const fetchUnions = async () => {
  try {
    const res = await fetch('https://raw.githubusercontent.com/nuhil/bangladesh-geocode/master/unions/unions.json')
    const result = await res.json()
    unions.value = result[2].data.filter(u => String(u.upazilla_id) === String(props.upazila.id))
  } catch (err) {
    console.error('Unions fetch error:', err)
  } finally {
    loading.value = false
  }
}

onMounted(() => {
  fetchUnions()
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