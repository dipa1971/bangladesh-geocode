<template>
  <div class="details-wrapper">
    <!-- হেডার -->
    <header class="header details-header">
      <span class="header-sun" aria-hidden="true"></span>
      <p class="header-eyebrow">বিস্তারিত তথ্য</p>
      <h1>{{ item.name }}</h1>
      <p class="header-sub">Bengali Name: {{ item.bn_name }}</p>
    </header>

    <!-- ব্রেডক্রাম্ব -->
    <div class="nav-tab">
      <span v-if="parentName" class="parent-link">{{ parentName }}</span>
      <span v-if="parentName" class="crumb-sep">›</span>
      <span class="crumb-current">{{ item.name }}</span>
    </div>

    <!-- কার্ড -->
    <div class="card details-card">
      <div class="details-card__topline"></div>

      <div class="info-grid">
        <div class="info-row">
          <span class="info-label">Bengali Name</span>
          <span class="info-value">{{ item.bn_name }}</span>
        </div>

        <div class="info-row" v-if="item.url">
          <span class="info-label">Website</span>
          <a :href="'https://' + item.url" target="_blank" class="info-link">{{ item.url }}</a>
        </div>

        <div class="info-row" v-if="item.lat && item.lon">
          <span class="info-label">Coordinates</span>
          <span class="info-value info-value--mono">{{ item.lat }}, {{ item.lon }}</span>
        </div>
      </div>

      <!-- ওপেনস্ট্রিটম্যাপ -->
      <div v-if="item.lat && item.lon" class="map-container">
        <p class="map-label">📍 মানচিত্রে অবস্থান</p>
        <iframe
          width="100%"
          height="350"
          frameborder="0"
          scrolling="no"
          marginheight="0"
          marginwidth="0"
          :src="`https://www.openstreetmap.org/export/embed.html?bbox=${Number(item.lon) - 0.08}%2C${Number(item.lat) - 0.08}%2C${Number(item.lon) + 0.08}%2C${Number(item.lat) + 0.08}&layer=mapnik&marker=${item.lat}%2C${item.lon}`"
        ></iframe>
      </div>

      <button class="btn-back-details" @click="$emit('back')">← Back</button>
    </div>
  </div>
</template>

<script setup>
defineProps({
  item: Object,
  parentName: String
})
</script>

<style scoped>
.details-wrapper {
  animation: fadeIn 0.35s ease;
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(8px); }
  to { opacity: 1; transform: translateY(0); }
}

/* Header — App.vue-এর সাথে মেলানো */
.details-header {
  padding: 44px 24px 40px;
  text-align: left;
}

.details-header h1 {
  font-size: 32px;
}

/* Breadcrumb */
.nav-tab {
  display: flex;
  align-items: center;
  gap: 6px;
}

.parent-link {
  color: #0f5c42;
  font-weight: 600;
}

.crumb-sep {
  color: #c9a24a;
  font-weight: 700;
}

.crumb-current {
  color: #c9a24a;
  font-weight: 700;
}

/* Card */
.details-card {
  max-width: 100%;
  padding: 36px;
  position: relative;
  overflow: hidden;
}

.details-card__topline {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  height: 5px;
  background: linear-gradient(90deg, #006a4e, #22c55e 55%, #c9a24a);
}

/* Info grid */
.info-grid {
  display: flex;
  flex-direction: column;
  gap: 4px;
  margin-bottom: 24px;
}

.info-row {
  display: flex;
  align-items: baseline;
  justify-content: space-between;
  gap: 16px;
  padding: 14px 4px;
  border-bottom: 1px solid rgba(0, 90, 54, 0.08);
  flex-wrap: wrap;
}

.info-row:last-child {
  border-bottom: none;
}

.info-label {
  font-size: 13px;
  font-weight: 700;
  letter-spacing: 0.03em;
  text-transform: uppercase;
  color: #0f5c42;
  flex: 0 0 auto;
}

.info-value {
  font-size: 16px;
  color: #1c2d24;
  font-weight: 500;
  text-align: right;
}

.info-value--mono {
  font-family: 'Courier New', monospace;
  font-size: 15px;
  color: #3f5e4d;
}

.info-link {
  color: #a9791f;
  font-weight: 600;
  text-decoration: none;
  word-break: break-all;
  text-align: right;
}

.info-link:hover {
  text-decoration: underline;
  color: #c9a24a;
}

/* Map */
.map-container {
  margin: 8px 0 26px;
  border-radius: 14px;
  overflow: hidden;
  border: 1px solid rgba(0, 90, 54, 0.12);
  box-shadow: 0 8px 22px rgba(0, 40, 25, 0.06);
}

.map-label {
  padding: 12px 16px;
  background: linear-gradient(135deg, #f0f7f3, #ffffff);
  font-size: 13.5px;
  font-weight: 600;
  color: #0f5c42;
  border-bottom: 1px solid rgba(0, 90, 54, 0.1);
}

.map-container iframe {
  display: block;
}

/* Back button — গাঢ় স্লেট, formal */
.btn-back-details {
  background: linear-gradient(135deg, #475569 0%, #26313f 100%);
  color: white;
  border: none;
  padding: 10px 22px;
  border-radius: 10px;
  cursor: pointer;
  font-size: 14px;
  font-weight: 600;
  box-shadow: 0 6px 16px rgba(30, 41, 59, 0.2);
  transition: all 0.25s ease;
}

.btn-back-details:hover {
  transform: translateX(-4px);
  background: linear-gradient(135deg, #334155, #1e293b);
}
</style>