<template>
  <div class="quran-page">
    <div class="max-w-3xl mx-auto px-4 pt-4">
      <button @click="goToDashboard"
        class="mb-4 px-4 py-2 bg-green-500 text-white rounded-lg hover:bg-green-600 transition-colors flex items-center">
        <span class="mr-2">←</span> Kembali ke Dashboard
      </button>
    </div>
    <h1 class="text-3xl font-bold text-center my-8">Al-Quran Digital</h1>
    <div class="max-w-3xl mx-auto px-4 py-8">
      <!-- Search functionality -->
      <div class="mb-6">
        <div class="flex gap-2">
          <input v-model="searchQuery" placeholder="Cari surah..."
            class="flex-grow p-3 border border-gray-300 rounded-lg shadow-sm focus:outline-none focus:ring-2 focus:ring-green-500 focus:border-transparent"
            @keyup.enter="searchSurah" />
          <button @click="searchSurah"
            class="px-4 py-2 bg-green-500 text-white rounded-lg hover:bg-green-600 transition-colors">
            Cari
          </button>
          <button v-if="isSearching" @click="clearSearch"
            class="px-4 py-2 bg-red-500 text-white rounded-lg hover:bg-red-600 transition-colors">
            Batal
          </button>
        </div>
        <p v-if="filteredSurahs.length && isSearching" class="mt-2 text-gray-600">
          Ditemukan {{ filteredSurahs.length }} surah
        </p>
      </div>

      <!-- Display mode selector when search is active -->
      <div v-if="isSearching" class="mb-6">
        <div class="flex items-center justify-center gap-2">
          <span class="text-gray-600">Melihat hasil pencarian</span>
          <button @click="clearSearch" class="text-green-500 underline hover:text-green-600">
            Kembali ke tampilan semua surah
          </button>
        </div>
      </div>

      <!-- Normal mode - Surah selection and display -->
      <div v-if="!isSearching">
        <select v-model="surahNumber" @change="fetchQuranData"
          class="w-full p-3 mb-6 border border-gray-300 rounded-lg shadow-sm focus:outline-none focus:ring-2 focus:ring-green-500 focus:border-transparent">
          <option v-for="surah in surahList" :key="surah.number" :value="surah.number">
            {{ surah.number }}. {{ surah.englishName }} ({{ surah.name }})
          </option>
        </select>

        <h2 class="text-2xl font-semibold text-center text-gray-700 mb-6">{{ surahName }}</h2>

        <div v-if="loading" class="text-center text-gray-600">
          <div class="animate-spin inline-block w-8 h-8 border-4 border-green-500 border-t-transparent rounded-full">
          </div>
          <p class="mt-2">Memuat...</p>
        </div>

        <div v-for="ayah in paginatedAyahs" :key="ayah.numberInSurah" class="mb-8 p-6 bg-white rounded-lg shadow-md">
          <p class="mb-4 text-right leading-loose text-xl">
            <span class="inline-block bg-green-500 text-white rounded-full w-8 h-8 text-center leading-8 mr-2">{{
              ayah.numberInSurah }}</span>
            {{ ayah.text }}
          </p>
          <p class="mb-4 text-gray-600">{{ translations[ayah.numberInSurah - 1]?.text }}</p>
          <audio controls class="w-full">
            <source :src="ayah.audio" type="audio/mpeg" />
          </audio>
        </div>

        <div class="flex items-center justify-center gap-4 mt-8">
          <button @click="prevPage" :disabled="currentPage === 1"
            class="px-4 py-2 bg-green-500 text-white rounded-lg disabled:bg-gray-300 disabled:cursor-not-allowed hover:bg-green-600 transition-colors">
            Sebelumnya
          </button>
          <span class="text-gray-600">Halaman {{ currentPage }} dari {{ totalPages }}</span>
          <button @click="nextPage" :disabled="currentPage === totalPages"
            class="px-4 py-2 bg-green-500 text-white rounded-lg disabled:bg-gray-300 disabled:cursor-not-allowed hover:bg-green-600 transition-colors">
            Berikutnya
          </button>
        </div>
      </div>

      <!-- Search results display -->
      <div v-if="isSearching">
        <h2 class="text-2xl font-semibold text-center text-gray-700 mb-6">Hasil Pencarian: "{{ searchQuery }}"</h2>

        <div v-if="loading" class="text-center text-gray-600">
          <div class="animate-spin inline-block w-8 h-8 border-4 border-green-500 border-t-transparent rounded-full">
          </div>
          <p class="mt-2">Mencari...</p>
        </div>

        <div v-if="filteredSurahs.length === 0 && !loading" class="text-center p-6 bg-white rounded-lg shadow-md">
          <p class="text-gray-600">Tidak ada surah yang ditemukan untuk pencarian "{{ searchQuery }}"</p>
        </div>

        <div v-if="filteredSurahs.length > 0" class="grid grid-cols-1 md:grid-cols-2 gap-4">
          <div v-for="surah in paginatedFilteredSurahs" :key="surah.number"
            class="p-4 bg-white rounded-lg shadow-md hover:shadow-lg transition-shadow cursor-pointer"
            @click="selectSurah(surah.number)">
            <div class="flex items-center justify-between">
              <div class="flex items-center">
                <span class="inline-flex items-center justify-center bg-green-500 text-white rounded-full w-8 h-8 mr-3">
                  {{ surah.number }}
                </span>
                <div>
                  <h3 class="font-bold">{{ surah.englishName }}</h3>
                  <p class="text-gray-600 text-sm">{{ surah.name }}</p>
                </div>
              </div>
              <div class="text-right">
                <p class="text-sm text-gray-500">{{ surah.revelationType }}</p>
                <p class="text-sm text-gray-500">{{ surah.numberOfAyahs }} Ayat</p>
              </div>
            </div>
          </div>
        </div>

        <div v-if="filteredSurahs.length > 0" class="flex items-center justify-center gap-4 mt-8">
          <button @click="prevSearchPage" :disabled="searchPage === 1"
            class="px-4 py-2 bg-green-500 text-white rounded-lg disabled:bg-gray-300 disabled:cursor-not-allowed hover:bg-green-600 transition-colors">
            Sebelumnya
          </button>
          <span class="text-gray-600">Halaman {{ searchPage }} dari {{ searchTotalPages }}</span>
          <button @click="nextSearchPage" :disabled="searchPage === searchTotalPages"
            class="px-4 py-2 bg-green-500 text-white rounded-lg disabled:bg-gray-300 disabled:cursor-not-allowed hover:bg-green-600 transition-colors">
            Berikutnya
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: 'Quran',
  data() {
    return {
      surahList: [], // List semua surah
      surahNumber: 1, // Surah default
      surahName: "",
      ayahs: [],
      translations: [],
      currentPage: 1,
      pageSize: 5, // Jumlah ayat per halaman
      loading: true,
      // Search related data
      searchQuery: "",
      isSearching: false,
      searchPage: 1,
      searchPageSize: 10,
    };
  },
  computed: {
    totalPages() {
      return Math.ceil(this.ayahs.length / this.pageSize);
    },
    paginatedAyahs() {
      const start = (this.currentPage - 1) * this.pageSize;
      return this.ayahs.slice(start, start + this.pageSize);
    },
    // Filter surahs based on search query
    filteredSurahs() {
      if (!this.searchQuery) return [];

      const query = this.searchQuery.toLowerCase();
      return this.surahList.filter(surah =>
        surah.name.toLowerCase().includes(query) ||
        surah.englishName.toLowerCase().includes(query) ||
        surah.englishNameTranslation.toLowerCase().includes(query) ||
        surah.number.toString() === query
      );
    },
    searchTotalPages() {
      return Math.ceil(this.filteredSurahs.length / this.searchPageSize);
    },
    paginatedFilteredSurahs() {
      const start = (this.searchPage - 1) * this.searchPageSize;
      return this.filteredSurahs.slice(start, start + this.searchPageSize);
    }
  },
  methods: {
    goToDashboard() {
      this.$router.push('/');
    },
    searchSurah() {
      if (!this.searchQuery.trim()) return;
      this.isSearching = true;
      this.searchPage = 1;
      this.loading = false;
    },
    clearSearch() {
      this.isSearching = false;
      this.searchQuery = "";
      this.searchPage = 1;
    },
    selectSurah(number) {
      this.surahNumber = number;
      this.clearSearch();
      this.fetchQuranData();
    },
    prevSearchPage() {
      if (this.searchPage > 1) this.searchPage--;
    },
    nextSearchPage() {
      if (this.searchPage < this.searchTotalPages) this.searchPage++;
    },
    // Existing methods remain unchanged
    async fetchSurahList() {
      try {
        const response = await axios.get("https://api.alquran.cloud/v1/surah");
        this.surahList = response.data.data; // Simpan semua daftar surah
      } catch (error) {
        console.error("Gagal mengambil daftar surah:", error);
      }
    },
    async fetchQuranData() {
      this.loading = true;
      try {
        const arabicResponse = await axios.get(`https://api.alquran.cloud/v1/surah/${this.surahNumber}`);
        const translationResponse = await axios.get(`https://api.alquran.cloud/v1/surah/${this.surahNumber}/id.indonesian`);
        const audioResponse = await axios.get(`https://api.alquran.cloud/v1/surah/${this.surahNumber}/ar.alafasy`);

        this.surahName = arabicResponse.data.data.englishName;
        this.ayahs = arabicResponse.data.data.ayahs.map((ayah, index) => ({
          numberInSurah: ayah.numberInSurah,
          text: ayah.text,
          audio: audioResponse.data.data.ayahs[index].audio
        }));
        this.translations = translationResponse.data.data.ayahs;
      } catch (error) {
        console.error("Error fetching data:", error);
      }
      this.loading = false;
      this.currentPage = 1; // Reset ke halaman pertama setiap ganti surah
    },
    prevPage() {
      if (this.currentPage > 1) this.currentPage--;
    },
    nextPage() {
      if (this.currentPage < this.totalPages) this.currentPage++;
    }
  },
  mounted() {
    this.fetchSurahList();
    this.fetchQuranData();
  }
};
</script>

<style>
/* Hapus semua style yang ada sebelumnya karena sudah menggunakan Tailwind CSS */
</style>
