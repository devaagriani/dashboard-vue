<script setup>
import { ref, computed, onMounted } from 'vue'
import UserCard from './components/UserCard.vue'

const users = ref([])
const isLoading = ref(false)
const errorMessage = ref('')
const queryPencarian = ref('')
const arahUrutan = ref('asc')

async function muatPengguna() {
  isLoading.value = true
  errorMessage.value = ''
  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/users')
    if (!response.ok) throw new Error('Gagal mengambil data pengguna')
    users.value = await response.json()
  } catch (err) {
    errorMessage.value = err.message
  } finally {
    isLoading.value = false
  }
}

// 1. Computed Filter Pencarian
const penggunaTersaring = computed(() => {
  const keyword = queryPencarian.value.toLowerCase().trim()
  if (!keyword) return users.value
  return users.value.filter(user => 
    user.username.toLowerCase().includes(keyword) ||
    user.name.toLowerCase().includes(keyword)
  )
})

// 2. Chained Computed (Sortir dari hasil penggunaTersaring)
const penggunaDiurutkan = computed(() => {
  return [...penggunaTersaring.value].sort((a, b) => {
    if (arahUrutan.value === 'asc') {
      return a.name.localeCompare(b.name)
    } else {
      return b.name.localeCompare(a.name)
    }
  })
})

onMounted(() => {
  muatPengguna()
})
</script>

<template>
  <div class="app-wrapper">
    <!-- Header Dark Teal -->
    <header class="app-header">
      <h1>Dashboard Vue — Pertemuan 5</h1>
    </header>

    <main class="main-content">
      <!-- Panel Kontrol Input & Tombol -->
      <div class="control-panel">
        <button @click="muatPengguna" class="btn-primary" :disabled="isLoading">
          {{ isLoading ? 'Memuat...' : 'Muat Pengguna' }}
        </button>

        <input 
          v-model="queryPencarian" 
          type="text" 
          placeholder="Cari username..." 
          class="search-input"
        />

        <div class="sort-group">
          <button 
            @click="arahUrutan = 'asc'" 
            :class="['btn-sort', { active: arahUrutan === 'asc' }]"
          >
            Urutkan A-Z
          </button>
          <button 
            @click="arahUrutan = 'desc'" 
            :class="['btn-sort', { active: arahUrutan === 'desc' }]"
          >
            Urutkan Z-A
          </button>
        </div>
      </div>

      <!-- State Loading / Error / Empty -->
      <div v-if="isLoading" class="state-card">Memuat data pengguna...</div>
      <div v-else-if="errorMessage" class="state-card error">{{ errorMessage }}</div>
      <div v-else-if="penggunaDiurutkan.length === 0" class="state-card">
        Tidak ada pengguna yang cocok.
      </div>

      <!-- Container Utama Daftar Pengguna (Border Luar Lengkung) -->
      <div v-else class="user-list-container">
        <UserCard 
          v-for="user in penggunaDiurutkan" 
          :key="user.id" 
          :user="user" 
        />
      </div>
    </main>
  </div>
</template>

<style>
/* Reset dasar */
* {
  box-sizing: border-box;
}

body {
  margin: 0;
  font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
  background-color: #f8fafc;
  color: #1e293b;
}

.app-wrapper {
  width: 100%;
}

.app-header {
  background-color: #0b506b;
  color: #ffffff;
  padding: 16px 32px;
}

.app-header h1 {
  margin: 0;
  font-size: 1.35rem;
  font-weight: 600;
}

.main-content {
  max-width: 900px;
  margin: 24px auto;
  padding: 0 16px;
}

.control-panel {
  display: flex;
  gap: 12px;
  margin-bottom: 20px;
  align-items: center;
  flex-wrap: wrap;
}

.btn-primary {
  background-color: #0b506b;
  color: #ffffff;
  border: none;
  padding: 8px 16px;
  border-radius: 6px;
  font-weight: 500;
  cursor: pointer;
  font-size: 0.9rem;
}

.search-input {
  padding: 8px 12px;
  border: 1px solid #d1d5db;
  border-radius: 6px;
  outline: none;
  font-size: 0.9rem;
  width: 220px;
}

.search-input:focus {
  border-color: #0b506b;
}

.sort-group {
  display: flex;
  gap: 8px;
}

.btn-sort {
  padding: 8px 14px;
  border: 1px solid #d1d5db;
  background-color: #ffffff;
  color: #374151;
  border-radius: 6px;
  font-size: 0.85rem;
  cursor: pointer;
}

.btn-sort.active {
  background-color: #0b506b;
  color: #ffffff;
  border-color: #0b506b;
}

/* Container utama menyatukan seluruh baris UserCard */
.user-list-container {
  border: 1px solid #e5e7eb;
  border-radius: 8px;
  overflow: hidden;
  background-color: #ffffff;
  box-shadow: 0 1px 2px rgba(0, 0, 0, 0.05);
}

.state-card {
  padding: 24px;
  text-align: center;
  background-color: #ffffff;
  border: 1px solid #e5e7eb;
  border-radius: 8px;
  color: #6b7280;
}

.state-card.error {
  color: #ef4444;
}
</style>