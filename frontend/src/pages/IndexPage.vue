<template>
  <q-page padding>
    <div class="text-h4 q-mb-md">
      Version Control + Docker Demo (Quasar)
    </div>


    <q-card class="q-mb-md">
      <q-card-section>
        <div class="text-h6">Git Workflow (ตัวอย่างขั้นตอนทำงาน)</div>
        <q-list bordered separator class="q-mt-sm">
          <q-item v-for="(step, index) in gitSteps" :key="index">
            <q-item-section avatar>
              <q-badge>{{ index + 1 }}</q-badge>
            </q-item-section>
            <q-item-section>
              <q-item-label>{{ step.title }}</q-item-label>
              <q-item-label caption>{{ step.detail }}</q-item-label>
            </q-item-section>
          </q-item>
        </q-list>
      </q-card-section>
    </q-card>


    <q-card>
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


    <q-card>
    <q-card-section>
      <div class="text-h6">Map</div>
      <div id="map" style="width: 100%; height: 600px;"></div>
      </q-card-section>
    </q-card>
  </q-page>

</template>


<script setup>
import { onMounted } from 'vue';
import L from 'leaflet';
import 'leaflet/dist/leaflet.css';
import iconUrl from 'leaflet/dist/images/marker-icon.png?url';
import iconShadowUrl from 'leaflet/dist/images/marker-shadow.png?url';

L.Icon.Default.mergeOptions({
  iconUrl,
  shadowUrl: iconShadowUrl,
  iconSize: [25, 41],
  iconAnchor: [12, 41]
});

  onMounted(() => {
    // 1. ระบุพิกัด
    const lat = 18.8946321;
    const lng = 99.0108616;

    // 2. สร้างแผนที่
    const map = L.map('map').setView([lat, lng], 10);

    // ใส่แผนที่พื้นหลัง (OpenStreetMap)
    L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
      attribution: '&copy; OpenStreetMap contributors'
    }).addTo(map);

    // 3. ใส่ Marker และ Label (ชื่อ สกุล รหัส)
    const myName = "นายพัทธดนย์ แก้วกัลยา"; // ใส่ชื่อคุณ
    const myID = "6604101358";     // ใส่รหัสนักศึกษาคุณ

    L.marker([lat, lng])
      .addTo(map)
      .bindPopup(`<b>ตำแหน่งของฉัน</b><br>ชื่อ: ${myName}<br>รหัส: ${myID}`)
      .openPopup();
  });


  const gitSteps = [
  {
    title: 'แก้โค้ด Quasar ใน src/',
    detail: 'เพิ่มหน้า / component ใหม่ เช่น IndexPage.vue, Layout ต่าง ๆ',
  },
  {
    title: 'ตรวจสอบสถานะไฟล์',
    detail: 'ใช้คำสั่ง `git status` ดูว่าไฟล์ไหนเปลี่ยนแปลงบ้าง',
  },
  {
    title: 'เพิ่มไฟล์เข้า staging',
    detail: 'ใช้ `git add .` หรือ `git add src/pages/IndexPage.vue`',
  },
  {
    title: 'สร้าง commit พร้อมข้อความแบบ Conventional Commits',
    detail: 'เช่น `feat: add Git workflow demo page`',
  },
  {
    title: 'push ขึ้น GitHub',
    detail: 'ใช้ `git push origin feature/quasar-demo` แล้วเปิด Pull Request',
  },
]


const dockerItems = [
  {
    title: 'Image',
    detail: 'แม่พิมพ์ของ container สร้างจาก Dockerfile เช่น image สำหรับ Quasar SPA',
  },
  {
    title: 'Container',
    detail: 'instance ของ image ที่กำลังรัน เช่น container ที่รัน Nginx เสิร์ฟไฟล์ของ Quasar',
  },
  {
    title: 'Volume',
    detail: 'ใช้เก็บข้อมูลถาวร เช่น log / ไฟล์ upload (frontend อาจไม่ใช้มากเท่า backend)',
  },
  {
    title: 'Network',
    detail: 'เชื่อม container ระหว่างกัน เช่น frontend คุยกับ backend ผ่าน network ของ Docker',
  },
]

</script>
