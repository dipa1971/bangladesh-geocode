<template>
  <div class="main-container">
    <!-- Decorative background motif -->
    <div class="bg-motif" aria-hidden="true"></div>

    <!-- Header -->
    <header class="header" v-if="currentView !== 'details'">
      <span class="header-sun" aria-hidden="true"></span>
      <p class="header-eyebrow">স্বাগতম • মাতৃভূমির ঠিকানা</p>
      <h1>Bangladesh Administrative Areas</h1>
      <p class="header-sub">Explore divisions, districts, upazilas, and unions of Bangladesh</p>
    </header>

    <div class="nav-tab" v-if="currentView !== 'details'">
      <button :class="{ 'active-link': currentView === 'divisions' }" @click="showDivisionsView">
        🏛️ Divisions
      </button>
      <span v-if="selectedDivision" class="crumb"> <span class="crumb-sep">›</span> <a href="#" @click.prevent="currentView = 'districts'">{{ selectedDivision.name }}</a></span>
      <span v-if="selectedDistrict" class="crumb"> <span class="crumb-sep">›</span> <a href="#" @click.prevent="currentView = 'upazilas'">{{ selectedDistrict.name }}</a></span>
      <span v-if="selectedUpazila" class="crumb"> <span class="crumb-sep">›</span> <span class="crumb-current">{{ selectedUpazila.name }}</span></span>
    </div>

    <!-- ব্যাক বাটন -->
    <button 
      v-if="currentView !== 'divisions' && currentView !== 'details'" 
      class="btn-back-main" 
      @click="goBack"
    >
      ← Back
    </button>

    <!-- ডাইনামিক ভিউসমূহ -->
    <DetailView 
      v-if="currentView === 'details'" 
      :item="selectedDetailItem" 
      :parentName="selectedDivision ? selectedDivision.name : ''"
      @back="goBackFromDetails" 
    />

    <DivisionList 
      v-else-if="currentView === 'divisions'" 
      @select-division="onSelectDivision" 
      @view-details="onViewDetails"
    />

    <DistrictList 
      v-else-if="currentView === 'districts'" 
      :division="selectedDivision" 
      @select-district="onSelectDistrict"
      @view-details="onViewDetails"
    />

    <UpazilaList 
      v-else-if="currentView === 'upazilas'" 
      :district="selectedDistrict" 
      @select-upazila="onSelectUpazila"
      @view-details="onViewDetails"
    />

    <UnionList 
      v-else-if="currentView === 'unions'" 
      :upazila="selectedUpazila" 
      @view-details="onViewDetails"
    />
  </div>
</template>

<script setup>
import { ref } from 'vue'
import DivisionList from './components/DivisionList.vue'
import DistrictList from './components/DistrictList.vue'
import UpazilaList from './components/UpazilaList.vue'
import UnionList from './components/UnionList.vue'
import DetailView from './components/DetailView.vue'

const currentView = ref('divisions')
const previousView = ref('divisions')

const selectedDivision = ref(null)
const selectedDistrict = ref(null)
const selectedUpazila = ref(null)
const selectedDetailItem = ref(null)

const onSelectDivision = (division) => {
  selectedDivision.value = division
  currentView.value = 'districts'
}

const onSelectDistrict = (district) => {
  selectedDistrict.value = district
  currentView.value = 'upazilas'
}

const onSelectUpazila = (upazila) => {
  selectedUpazila.value = upazila
  currentView.value = 'unions'
}

const goBack = () => {
  if (currentView.value === 'unions') {
    currentView.value = 'upazilas'
    selectedUpazila.value = null
  } else if (currentView.value === 'upazilas') {
    currentView.value = 'districts'
    selectedDistrict.value = null
  } else if (currentView.value === 'districts') {
    currentView.value = 'divisions'
    selectedDivision.value = null
  }
}

const onViewDetails = (item) => {
  selectedDetailItem.value = item
  previousView.value = currentView.value
  currentView.value = 'details'
}

const goBackFromDetails = () => {
  currentView.value = previousView.value
}

const showDivisionsView = () => {
  currentView.value = 'divisions'
  selectedDivision.value = null
  selectedDistrict.value = null
  selectedUpazila.value = null
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Hind+Siliguri:wght@400;500;600;700&family=Baloo+Da+2:wght@600;700;800&display=swap');

/* ১. গ্লোবাল বেস — উষ্ণ মাটির টোন + সবুজ ছায়া */
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: 'Hind Siliguri', 'Segoe UI', Roboto, sans-serif;
  background: #f7f4ec;
  color: #1c2d24;
  min-height: 100vh;
}

.main-container {
  position: relative;
  width: 100%;
  max-width: 1320px;
  margin: 0 auto;
  padding: 32px 20px 60px;
}

/* Subtle nokshi-kantha style dotted texture, behind everything */
.bg-motif {
  position: fixed;
  inset: 0;
  z-index: -1;
  background:
    radial-gradient(circle at 15% 20%, rgba(0, 106, 78, 0.05) 0, transparent 45%),
    radial-gradient(circle at 85% 75%, rgba(244, 42, 65, 0.045) 0, transparent 45%),
    repeating-linear-gradient(45deg, rgba(0, 106, 78, 0.025) 0 2px, transparent 2px 26px);
}

/* ২. হেডার — পতাকার সবুজ জমিন, লাল সূর্য, স্বর্ণাভ বিন্দু */
.header {
  background: linear-gradient(160deg, #00754f 0%, #005a36 55%, #003d24 100%);
  color: #ffffff;
  text-align: center;
  padding: 52px 24px 46px;
  border-radius: 22px;
  margin-bottom: 26px;
  box-shadow: 0 18px 40px rgba(0, 66, 37, 0.28);
  position: relative;
  overflow: hidden;
  border: 1px solid rgba(255, 255, 255, 0.12);
}

.header::after {
  content: '';
  position: absolute;
  inset: 0;
  background-image: repeating-linear-gradient(115deg, rgba(255,255,255,0.035) 0 1px, transparent 1px 22px);
  pointer-events: none;
}

/* জাতীয় পতাকার লাল বৃত্ত — সূর্যোদয়ের প্রতীক */
.header-sun {
  position: absolute;
  top: -50px;
  right: 6%;
  width: 150px;
  height: 150px;
  border-radius: 50%;
  background: radial-gradient(circle at 35% 35%, #ff5b6e 0%, #f42a41 55%, rgba(244,42,65,0) 78%);
  box-shadow: 0 0 60px 10px rgba(244, 42, 65, 0.35);
}

.header-eyebrow {
  position: relative;
  font-size: 13px;
  font-weight: 600;
  letter-spacing: 0.12em;
  text-transform: uppercase;
  color: #ffd77a;
  margin-bottom: 10px;
}

.header h1 {
  position: relative;
  font-family: 'Baloo Da 2', 'Hind Siliguri', sans-serif;
  font-size: 40px;
  font-weight: 800;
  letter-spacing: 0.3px;
  margin-bottom: 10px;
  text-shadow: 0 3px 10px rgba(0, 0, 0, 0.25);
}

.header-sub {
  position: relative;
  opacity: 0.94;
  font-size: 16.5px;
  color: #d9f5e6;
  font-weight: 400;
}

/* ৩. ব্রেডক্রাম্ব ন্যাভ — স্বর্ণাভ বর্ডার সহ পিল */
.nav-tab {
  background: #ffffff;
  padding: 14px 24px;
  border-radius: 999px;
  margin-bottom: 26px;
  font-size: 15px;
  color: #4a6356;
  border: 1px solid rgba(0, 90, 54, 0.12);
  box-shadow: 0 6px 18px rgba(0, 66, 37, 0.06);
  display: flex;
  align-items: center;
  flex-wrap: wrap;
  gap: 4px;
}

.nav-tab button {
  background: linear-gradient(135deg, #006a4e, #00543e);
  color: #ffffff;
  border: none;
  padding: 7px 16px;
  border-radius: 999px;
  font-size: 14px;
  font-weight: 700;
  cursor: pointer;
  transition: all 0.2s ease;
}

.nav-tab button:hover {
  background: linear-gradient(135deg, #008763, #006a4e);
  transform: translateY(-1px);
}

.crumb-sep {
  color: #c9a24a;
  font-weight: 700;
  margin: 0 2px;
}

.nav-tab a {
  color: #0f5c42;
  font-weight: 600;
  text-decoration: none;
  transition: color 0.2s ease;
}

.nav-tab a:hover {
  color: #f42a41;
  text-decoration: underline;
}

.crumb-current {
  color: #c9a24a;
  font-weight: 700;
}

/* ৪. ব্যাক বাটন */
.btn-back-main {
  background: linear-gradient(135deg, #475569 0%, #26313f 100%);
  color: white;
  border: none;
  padding: 9px 20px;
  border-radius: 10px;
  cursor: pointer;
  font-size: 14px;
  font-weight: 600;
  margin-bottom: 22px;
  display: inline-flex;
  align-items: center;
  gap: 6px;
  box-shadow: 0 6px 16px rgba(30, 41, 59, 0.2);
  transition: all 0.25s ease;
}

.btn-back-main:hover {
  transform: translateX(-4px);
  background: linear-gradient(135deg, #334155, #1e293b);
}

/* ৫. সেকশন টাইটেল */
.section-title {
  color: #003d24;
  position: relative;
  padding-bottom: 14px;
  margin-bottom: 30px;
  font-family: 'Baloo Da 2', sans-serif;
  font-size: 27px;
  font-weight: 700;
}

.section-title::after {
  content: '';
  position: absolute;
  bottom: 0;
  left: 0;
  width: 78px;
  height: 5px;
  border-radius: 3px;
  background: linear-gradient(90deg, #f42a41 0%, #006a4e 60%, #c9a24a 100%);
}

/* ৬. কার্ড গ্রিড — উষ্ণ, প্রিমিয়াম, স্বর্ণাভ ছোঁয়া */
.cards-grid {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 24px;
}

@media (max-width: 1024px) {
  .cards-grid { grid-template-columns: repeat(2, 1fr); }
}

@media (max-width: 640px) {
  .cards-grid { grid-template-columns: 1fr; }
}

.card {
  background: #ffffff;
  border-radius: 18px;
  padding: 28px;
  border: 1px solid rgba(0, 90, 54, 0.09);
  box-shadow: 0 8px 24px rgba(0, 40, 25, 0.06);
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  position: relative;
  overflow: hidden;
}

.card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 5px;
  background: linear-gradient(90deg, #006a4e, #22c55e 55%, #c9a24a);
}

.card:hover {
  transform: translateY(-7px);
  box-shadow: 0 20px 40px rgba(0, 90, 54, 0.16);
  border-color: rgba(0, 106, 78, 0.3);
}

.card h3 {
  color: #0f291e;
  font-family: 'Baloo Da 2', sans-serif;
  font-size: 22px;
  margin-bottom: 12px;
  font-weight: 700;
}

.bengali-text {
  color: #3f5e4d;
  margin-bottom: 10px;
  font-size: 15px;
  font-weight: 500;
}

.website-link {
  margin-bottom: 22px;
  font-size: 14px;
}

.website-link a {
  color: #0284c7;
  text-decoration: none;
  font-weight: 500;
  word-break: break-all;
}

.website-link a:hover {
  text-decoration: underline;
}

/* ৭. বাটনসমূহ */
.card-buttons {
  display: flex;
  gap: 10px;
  flex-wrap: wrap;
}

.btn-green {
  background: linear-gradient(135deg, #006a4e 0%, #003d24 100%);
  color: white;
  border: none;
  padding: 11px 16px;
  border-radius: 10px;
  cursor: pointer;
  font-size: 14px;
  font-weight: 600;
  flex: 1;
  box-shadow: 0 4px 14px rgba(0, 106, 78, 0.25);
  transition: all 0.2s ease;
}

.btn-green:hover {
  background: linear-gradient(135deg, #00915f 0%, #006a4e 100%);
  box-shadow: 0 6px 18px rgba(0, 106, 78, 0.35);
  transform: translateY(-1px);
}

.btn-blue {
  background: linear-gradient(135deg, #c9a24a 0%, #a9791f 100%);
  color: #fffdf5;
  border: none;
  padding: 11px 16px;
  border-radius: 10px;
  cursor: pointer;
  font-size: 14px;
  font-weight: 600;
  box-shadow: 0 4px 14px rgba(169, 121, 31, 0.25);
  transition: all 0.2s ease;
}

.btn-blue:hover {
  background: linear-gradient(135deg, #d9b25e 0%, #c9a24a 100%);
  box-shadow: 0 6px 18px rgba(169, 121, 31, 0.35);
  transform: translateY(-1px);
}

/* ৮. লোডিং স্টেট */
.loading {
  text-align: center;
  font-size: 18px;
  font-weight: 600;
  color: #006a4e;
  padding: 60px;
}
</style>