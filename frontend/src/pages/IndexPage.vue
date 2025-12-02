<template>
  <q-page padding>
    <div class="text-h4 q-mb-md">
      Advanced Full-Stack Demo (Quasar + Express)
    </div>

    <!-- Git Workflow -->
    <q-card class="q-mb-md">
      <q-card-section>
        <div class="text-h6">Git Workflow (ตัวอย่างขั้นตอนทำงาน)</div>
        <q-list bordered separator class="q-mt-sm">
          <q-item v-for="(step, index) in gitSteps" :key="index">
            <q-item-section avatar>
              <q-badge color="primary">{{ index + 1 }}</q-badge>
            </q-item-section>
            <q-item-section>
              <q-item-label>{{ step.title }}</q-item-label>
              <q-item-label caption>{{ step.detail }}</q-item-label>
            </q-item-section>
          </q-item>
        </q-list>
      </q-card-section>
    </q-card>

    <!-- Docker Concepts -->
    <q-card class="q-mb-md">
      <q-card-section>
        <div class="text-h6">Docker Concepts (สรุปสั้น ๆ)</div>
        <q-list bordered separator class="q-mt-sm">
          <q-item v-for="(item, index) in dockerItems" :key="index">
            <q-item-section>
              <q-item-label>{{ item.title }}</q-item-label>
              <q-item-label caption>{{ item.detail }}</q-item-label>
            </q-item-section>
          </q-item>
        </q-list>
      </q-card-section>
    </q-card>

    <!-- API Data from Backend -->
    <q-card class="q-mb-md">
      <q-card-section>
        <div class="text-h6">Data from Backend API</div>
        <q-spinner v-if="loading" color="secondary" size="2em" />
        <q-list v-else bordered separator class="q-mt-sm">
          <q-item>
            <q-item-section>
              <q-item-label>Advanced Git Info</q-item-label>
              <!-- แสดงข้อมูลที่ดึงจาก Backend API -->
              <q-item-label caption>{{ apiData.git.detail || 'Loading...' }}</q-item-label>
            </q-item-section>
          </q-item>
          <q-item>
            <q-item-section>
              <q-item-label>Advanced Docker Info</q-item-label>
              <!-- แสดงข้อมูลที่ดึงจาก Backend API -->
              <q-item-label caption>{{ apiData.docker.detail || 'Loading...' }}</q-item-label>
            </q-item-section>
          </q-item>
        </q-list>
        <q-btn v-if="!loading" color="positive" @click="fetchData" class="q-mt-md">Refresh API Data</q-btn>
      </q-card-section>
    </q-card>

    <!-- Map Section -->
    <q-card>
      <q-card-section>
        <div class="text-h6">Map (Leaflet Demonstration)</div>
        <div id="map" style="width: 100%; height: 400px; border-radius: 8px;" class="q-mt-sm shadow-2"></div>
      </q-card-section>
    </q-card>
  </q-page>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';
import L from 'leaflet';
import 'leaflet/dist/leaflet.css';
// Leaflet จะต้องใช้ URL ของ Asset ต่างๆ (เช่น Marker Icon)
// ใน Quasar/Vite เราต้อง import ด้วย '?url' เพื่อให้ Vite จัดการ Path ให้ถูกต้อง
import iconUrl from 'leaflet/dist/images/marker-icon.png?url';
import iconShadowUrl from 'leaflet/dist/images/marker-shadow.png?url';

// แก้ปัญหา Icon Marker ไม่แสดงผลใน Leaflet
L.Icon.Default.mergeOptions({
  iconUrl,
  shadowUrl: iconShadowUrl,
  iconSize: [25, 41],
  iconAnchor: [12, 41]
});

// ข้อมูล Git Steps
const gitSteps = [
  { title: 'แก้โค้ด Quasar ใน src/', detail: 'เพิ่มหน้า / component ใหม่ เช่น IndexPage.vue, Layout ต่าง ๆ' },
  { title: 'ตรวจสอบสถานะไฟล์', detail: 'ใช้คำสั่ง `git status` ดูว่าไฟล์ไหนเปลี่ยนแปลงบ้าง' },
  { title: 'เพิ่มไฟล์เข้า staging', detail: 'ใช้ `git add .` หรือ `git add src/pages/IndexPage.vue`' },
  { title: 'สร้าง commit พร้อมข้อความแบบ Conventional Commits', detail: 'เช่น `feat: add Git workflow demo page`' },
  { title: 'push ขึ้น GitHub', detail: 'ใช้ `git push origin feature/quasar-demo` แล้วเปิด Pull Request' },
];

// ข้อมูล Docker Concepts
const dockerItems = [
  { title: 'Image', detail: 'แม่พิมพ์ของ container สร้างจาก Dockerfile เช่น image สำหรับ Quasar SPA' },
  { title: 'Container', detail: 'instance ของ image ที่กำลังรัน เช่น container ที่รัน Nginx เสิร์ฟไฟล์ของ Quasar' },
  { title: 'Volume', detail: 'ใช้เก็บข้อมูลถาวร เช่น log / ไฟล์ upload (frontend อาจไม่ใช้มากเท่า backend)' },
  { title: 'Network', detail: 'เชื่อม container ระหว่างกัน เช่น frontend คุยกับ backend ผ่าน network ของ Docker' },
];

// State สำหรับ API
const apiData = ref({ git: {}, docker: {} });
const loading = ref(true);

// ฟังก์ชันดึงข้อมูล API
const fetchData = async () => {
  loading.value = true;
  try {
    // ใช้ Environment Variable VITE_API_URL ในการกำหนด Base URL ของ Express Server
    const apiUrl = import.meta.env.VITE_API_URL || ''; 
    const response = await axios.get(apiUrl + '/api/demo');
    apiData.value = response.data;
  } catch (error) {
    console.error('API Error: ไม่สามารถเชื่อมต่อกับ Backend ได้', error);
    apiData.value = {
        git: { detail: 'ไม่สามารถดึงข้อมูล API ได้ (ตรวจสอบ Backend Server และ VITE_API_URL)' },
        docker: { detail: 'API Endpoint: /api/demo' }
    };
  } finally {
    loading.value = false;
  }
};

// ฟังก์ชันเริ่มต้น Map
const initMap = () => {
  // 1. ระบุพิกัด (มหาวิทยาลัยพายัพ, เชียงใหม่)
  const lat = 18.8946321;
  const lng = 99.0108616;

  // 2. สร้างแผนที่
  const map = L.map('map').setView([lat, lng], 14);

  // ใส่แผนที่พื้นหลัง (OpenStreetMap)
  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    attribution: '&copy; <a href="http://osm.org/copyright">OpenStreetMap</a> contributors'
  }).addTo(map);

  // 3. ใส่ Marker และ Label
  const myName = "นายพัทธดนย์ แก้วกัลยา";
  const myID = "6604101358";

  L.marker([lat, lng])
    .addTo(map)
    .bindPopup(`<b>ตำแหน่งของฉัน</b><br>ชื่อ: ${myName}<br>รหัส: ${myID}`)
    .openPopup();
};

onMounted(() => {
  // โหลด Map
  initMap(); 
  // ดึงข้อมูล API ทันที
  fetchData(); 
});
</script>