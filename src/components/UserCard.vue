<script setup>
import { ref } from 'vue'

defineProps({
  user: {
    type: Object,
    required: true
  }
})

// Problem 1: State lokal untuk buka/tutup detail
const isDetailOpen = ref(false)

function toggleDetail() {
  isDetailOpen.value = !isDetailOpen.value
}
</script>

<template>
  <div class="user-card">
    <div class="user-header">
      <div class="user-info">
        <h3 class="user-name">{{ user.name }}</h3>
        <p class="user-email">{{ user.email }}</p>
      </div>

      <button 
        @click="toggleDetail" 
        :class="['btn-toggle', { active: isDetailOpen }]"
      >
        {{ isDetailOpen ? 'Sembunyikan' : 'Lihat Detail' }}
      </button>
    </div>

    <!-- Box Detail dengan background abu-abu seperti di gambar -->
    <div v-if="isDetailOpen" class="user-detail-box">
      <p><strong>Telepon:</strong> {{ user.phone }}</p>
      <p><strong>Perusahaan:</strong> {{ user.company?.name || '-' }}</p>
      <p><strong>Kota:</strong> {{ user.address?.city || '-' }}</p>
    </div>
  </div>
</template>

<style scoped>
.user-card {
  padding: 16px 20px;
  border-bottom: 1px solid #e5e7eb;
  background-color: #ffffff;
}

.user-card:last-child {
  border-bottom: none;
}

.user-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
}

.user-name {
  margin: 0;
  font-size: 1rem;
  font-weight: 700;
  color: #1f2937;
}

.user-email {
  margin: 2px 0 0 0;
  font-size: 0.875rem;
  color: #6b7280;
}

/* Styling Tombol persis gambar */
.btn-toggle {
  padding: 6px 14px;
  font-size: 0.85rem;
  font-weight: 500;
  border-radius: 6px;
  cursor: pointer;
  background-color: #ffffff;
  color: #0b506b;
  border: 1px solid #0b506b;
  transition: all 0.15s ease-in-out;
}

.btn-toggle:hover {
  background-color: #f0f7fa;
}

/* Kapan status Sembunyikan (Aktif) */
.btn-toggle.active {
  background-color: #0b506b;
  color: #ffffff;
  border: 1px solid #0b506b;
}

/* Box Informasi Tambahan */
.user-detail-box {
  margin-top: 12px;
  padding: 12px 16px;
  background-color: #f4f7f9;
  border-radius: 8px;
  font-size: 0.875rem;
  color: #334155;
  line-height: 1.5;
}

.user-detail-box p {
  margin: 3px 0;
}
</style>