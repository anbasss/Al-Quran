<template>
    <div class="dashboard">
        <div class="container mx-auto px-6 sm:px-8 py-8 w-full max-w-8xl">
            <!-- Hero Section -->
            <div class="flex flex-col md:flex-row items-center justify-between min-h-screen">
                <div class="md:w-1/2 pr-4 md:pr-8 pl-2 md:pl-4">
                    <h1 class="text-5xl md:text-6xl font-bold text-gray-800 mb-8 leading-tight">
                        Baca & Pelajari<br />
                        <span class="text-green-600">Al-Quran Digital</span><br />
                        dengan Mudah
                    </h1>
                    <p class="text-gray-600 text-xl mb-10 leading-relaxed max-w-xl">
                        Platform digital untuk membaca, mempelajari, dan memahami Al-Quran dengan mudah.
                        Dilengkapi dengan terjemahan Bahasa Indonesia yang akurat.
                    </p>
                    <div class="space-x-4 ml-1">
                        <router-link to="/quran"
                            class="bg-green-600 text-white px-10 py-4 rounded-lg hover:bg-green-700 transition-colors duration-300 inline-block text-lg font-semibold shadow-lg hover:shadow-xl">
                            Mulai Membaca
                        </router-link>
                    </div>
                </div>
                <div class="md:w-1/2 relative px-4">
                    <div class="w-full h-[500px] relative">
                        <img src="/public/alquran.png" alt="Al-Quran Digital" class="w-full h-full object-contain md:object-cover
                                  md:-right-8 lg:-right-12 xl:-right-16
                                  transform md:scale-105 lg:scale-100
                                  transition-transform duration-300
                                  max-w-[550px] mx-auto">
                    </div>
                </div>
            </div>

            <!-- Features Section -->
            <div id="features" class="grid md:grid-cols-3 gap-6 md:gap-8 py-16 px-2 md:px-6">
                <div class="bg-white rounded-xl p-6 md:p-8 shadow-lg hover:shadow-xl transition-shadow duration-300">
                    <div class="text-green-600 text-4xl mb-4">
                        <i class="fas fa-book-open"></i>
                    </div>
                    <h3 class="text-2xl font-bold mb-4">Mudah Dibaca</h3>
                    <p class="text-gray-600 text-lg">
                        Tampilan yang nyaman dan responsif untuk membaca Al-Quran di berbagai perangkat
                    </p>
                </div>
                <div class="bg-white rounded-xl p-6 md:p-8 shadow-lg hover:shadow-xl transition-shadow duration-300">
                    <div class="text-green-600 text-4xl mb-4">
                        <i class="fas fa-language"></i>
                    </div>
                    <h3 class="text-2xl font-bold mb-4">Terjemahan Lengkap</h3>
                    <p class="text-gray-600 text-lg">
                        Dilengkapi dengan terjemahan Bahasa Indonesia yang akurat dan mudah dipahami
                    </p>
                </div>
                <div class="bg-white rounded-xl p-6 md:p-8 shadow-lg hover:shadow-xl transition-shadow duration-300">
                    <div class="text-green-600 text-4xl mb-4">
                        <i class="fas fa-mobile-alt"></i>
                    </div>
                    <h3 class="text-2xl font-bold mb-4">Responsif</h3>
                    <p class="text-gray-600 text-lg">
                        Dapat diakses melalui berbagai perangkat dengan tampilan yang optimal
                    </p>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    name: 'Dashboard',
}
</script>

<style scoped>
.dashboard {
    min-height: 100vh;
    width: 100%;
    background: linear-gradient(135deg, #ffffff 0%, #f8f9fa 100%);
    position: relative;
    overflow-x: hidden;
    overflow-y: auto;
    padding: 0 10px;
}

.dashboard::before {
    content: '';
    position: absolute;
    top: 0;
    right: 0;
    width: 45%;
    height: 100%;
    background: linear-gradient(135deg, rgba(34, 197, 94, 0.05) 0%, rgba(34, 197, 94, 0.1) 100%);
    clip-path: polygon(30% 0%, 100% 0%, 100% 100%, 0% 100%);
    z-index: 0;
}

.container {
    position: relative;
    z-index: 1;
    min-height: 100%;
}

@media (max-width: 768px) {
    .dashboard::before {
        width: 100%;
        height: 50%;
        clip-path: polygon(0 0, 100% 0, 100% 100%, 0 85%);
    }

    .dashboard {
        padding: 0 5px;
    }
}
</style>
