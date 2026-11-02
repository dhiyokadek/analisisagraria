<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dashboard Analisis Agraria & Kelautan</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Chart.js CDN -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/3.9.1/chart.min.js"></script>
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Google Fonts Inter -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #0f172a;
            color: #f8fafc;
        }
        .glass-card {
            background: rgba(30, 41, 59, 0.75);
            backdrop-filter: blur(12px);
            border: 1px solid rgba(255, 255, 255, 0.08);
        }
        .custom-scrollbar::-webkit-scrollbar {
            width: 6px;
            height: 6px;
        }
        .custom-scrollbar::-webkit-scrollbar-track {
            background: rgba(15, 23, 42, 0.6);
        }
        .custom-scrollbar::-webkit-scrollbar-thumb {
            background: rgba(100, 116, 139, 0.5);
            border-radius: 3px;
        }
    </style>
</head>
<body class="min-h-screen flex flex-col bg-slate-900 text-slate-100 custom-scrollbar">

    <!-- Header & Status Bar -->
    <header class="glass-card sticky top-0 z-50 border-b border-slate-800 px-4 lg:px-8 py-3.5">
        <div class="max-w-7xl mx-auto flex flex-col md:flex-row md:items-center md:justify-between gap-3">
            <div class="flex items-center gap-3">
                <div class="w-10 h-10 rounded-xl bg-gradient-to-br from-emerald-500 to-teal-700 flex items-center justify-center text-white font-bold text-xl shadow-lg shadow-emerald-500/20">
                    <i class="fa-solid fa-chart-pie"></i>
                </div>
                <div>
                    <h1 class="text-lg font-bold tracking-tight text-white flex items-center gap-2">
                        Dashboard Analisis Agraria & Kelautan
                        <span id="liveBadge" class="inline-flex items-center gap-1 text-[10px] px-2 py-0.5 rounded-full bg-emerald-500/20 text-emerald-400 border border-emerald-500/30">
                            <span class="w-1.5 h-1.5 rounded-full bg-emerald-400 animate-pulse"></span> Auto-Sync
                        </span>
                    </h1>
                    <p class="text-xs text-slate-400" id="lastUpdateText">Terakhir diperbarui: Memuat data...</p>
                </div>
            </div>

            <!-- Auto-Refresh Controls -->
            <div class="flex items-center gap-3">
                <div class="text-right hidden sm:block">
                    <span class="text-[11px] text-slate-400 block">Pembaruan berikutnya:</span>
                    <span id="timerText" class="text-xs font-mono font-semibold text-emerald-400">05:00</span>
                </div>
                <button id="btnRefresh" onclick="loadDashboardData(true)" class="px-3.5 py-2 bg-emerald-600 hover:bg-emerald-500 text-white rounded-xl text-xs font-medium transition flex items-center gap-2 shadow-sm active:scale-95">
                    <i class="fa-solid fa-rotate-right" id="refreshIcon"></i> Refresh Data
                </button>
            </div>
        </div>
    </header>

    <main class="flex-1 max-w-7xl w-full mx-auto p-4 lg:p-8 space-y-6">

        <!-- Banner Info Tab -->
        <div id="tabSourceBanner" class="p-4 rounded-2xl border border-slate-800 bg-slate-800/40 flex flex-col sm:flex-row items-start sm:items-center justify-between gap-3 text-xs">
            <div class="flex items-center gap-3">
                <i class="fa-solid fa-layer-group text-teal-400 text-lg"></i>
                <div>
                    <span class="font-semibold text-slate-200">Tab Terdeteksi & Tergabung:</span>
                    <span id="tabListBadge" class="text-slate-400 ml-1">Tabulasi</span>
                </div>
            </div>
        </div>

        <!-- Global Cascading Filters Bar -->
        <section class="glass-card p-5 rounded-2xl border border-slate-800 space-y-4">
            <div class="flex items-center justify-between">
                <h2 class="text-xs font-bold text-slate-300 uppercase tracking-wider flex items-center gap-2">
                    <i class="fa-solid fa-filter text-emerald-400"></i> Filter Bertingkat (Cascading Filter)
                </h2>
                <div class="flex items-center gap-2">
                    <span id="recordCountText" class="text-xs px-2.5 py-1 rounded-full bg-slate-800 border border-slate-700 text-slate-300 font-mono">0 Data Terfilter</span>
                    <button onclick="resetFilters()" class="px-3 py-1 bg-slate-800 hover:bg-slate-700 border border-slate-700 text-slate-400 hover:text-white rounded-lg text-xs transition flex items-center gap-1.5" title="Reset Filter">
                        <i class="fa-solid fa-arrow-rotate-left"></i> Reset
                    </button>
                </div>
            </div>
            
            <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-5 gap-3 text-xs">
                <div>
                    <label class="block text-slate-400 mb-1">Sumber Tab</label>
                    <select id="filterTab" onchange="onFilterChange('filterTab')" class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3 py-2 text-slate-200 focus:outline-none focus:border-emerald-500">
                        <option value="ALL">Semua Tab</option>
                    </select>
                </div>
                <div>
                    <label class="block text-slate-400 mb-1 font-semibold text-emerald-400">Kabupaten</label>
                    <select id="filterKabupaten" onchange="onFilterChange('filterKabupaten')" class="w-full bg-slate-900 border border-emerald-600/50 rounded-xl px-3 py-2 text-slate-200 focus:outline-none focus:border-emerald-500">
                        <option value="ALL">Semua Kabupaten</option>
                    </select>
                </div>
                <div>
                    <label class="block text-slate-400 mb-1 font-semibold text-teal-400">Kecamatan</label>
                    <select id="filterKecamatan" onchange="onFilterChange('filterKecamatan')" class="w-full bg-slate-900 border border-teal-600/50 rounded-xl px-3 py-2 text-slate-200 focus:outline-none focus:border-teal-500">
                        <option value="ALL">Semua Kecamatan</option>
                    </select>
                </div>
                <div>
                    <label class="block text-slate-400 mb-1 font-semibold text-sky-400">Desa / Banjar</label>
                    <select id="filterDesa" onchange="onFilterChange('filterDesa')" class="w-full bg-slate-900 border border-sky-600/50 rounded-xl px-3 py-2 text-slate-200 focus:outline-none focus:border-sky-500">
                        <option value="ALL">Semua Desa</option>
                    </select>
                </div>
                <div>
                    <label class="block text-slate-400 mb-1">Jenis Usaha</label>
                    <select id="filterJenisUsaha" onchange="onFilterChange('filterJenisUsaha')" class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3 py-2 text-slate-200 focus:outline-none focus:border-emerald-500">
                        <option value="ALL">Semua Jenis Usaha</option>
                    </select>
                </div>
                <div>
                    <label class="block text-slate-400 mb-1">Jenis KUB</label>
                    <select id="filterJenisKub" onchange="onFilterChange('filterJenisKub')" class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3 py-2 text-slate-200 focus:outline-none focus:border-emerald-500">
                        <option value="ALL">Semua KUB</option>
                    </select>
                </div>
                <div>
                    <label class="block text-slate-400 mb-1">Komoditas Usaha</label>
                    <select id="filterKomoditas" onchange="onFilterChange('filterKomoditas')" class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3 py-2 text-slate-200 focus:outline-none focus:border-emerald-500">
                        <option value="ALL">Semua Komoditas</option>
                    </select>
                </div>
                <div>
                    <label class="block text-slate-400 mb-1 font-semibold text-purple-400">Model Pemberdayaan</label>
                    <select id="filterModelPemberdayaan" onchange="onFilterChange('filterModelPemberdayaan')" class="w-full bg-slate-900 border border-purple-600/50 rounded-xl px-3 py-2 text-slate-200 focus:outline-none focus:border-purple-500">
                        <option value="ALL">Semua Model</option>
                    </select>
                </div>
                <div>
                    <label class="block text-slate-400 mb-1">Asal Sertipikat</label>
                    <select id="filterSertipikat" onchange="onFilterChange('filterSertipikat')" class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3 py-2 text-slate-200 focus:outline-none focus:border-emerald-500">
                        <option value="ALL">Semua Skema</option>
                    </select>
                </div>
                <div>
                    <label class="block text-slate-400 mb-1">Kendala / HKM</label>
                    <select id="filterHkm" onchange="onFilterChange('filterHkm')" class="w-full bg-slate-900 border border-slate-700 rounded-xl px-3 py-2 text-slate-200 focus:outline-none focus:border-emerald-500">
                        <option value="ALL">Semua Kendala</option>
                    </select>
                </div>
                <div class="sm:col-span-2 md:col-span-1 lg:col-span-2">
                    <label class="block text-slate-400 mb-1">Cari Kata Kunci</label>
                    <div class="relative">
                        <input type="text" id="searchInput" oninput="onFilterChange('searchInput')" placeholder="Nama, NIK, alamat..." class="w-full bg-slate-900 border border-slate-700 rounded-xl pl-8 pr-3 py-2 text-slate-200 focus:outline-none focus:border-emerald-500">
                        <i class="fa-solid fa-magnifying-glass absolute left-2.5 top-2.5 text-slate-500"></i>
                    </div>
                </div>
            </div>
        </section>

        <!-- KPI Cards Grid -->
        <section class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-4 gap-4">
            <!-- Total Penerima -->
            <div class="glass-card p-5 rounded-2xl border border-slate-800 relative overflow-hidden">
                <div class="flex items-center justify-between mb-2">
                    <span class="text-xs text-slate-400 font-medium">Jumlah Penerima</span>
                    <span class="w-8 h-8 rounded-lg bg-emerald-500/20 text-emerald-400 flex items-center justify-center text-sm">
                        <i class="fa-solid fa-users"></i>
                    </span>
                </div>
                <div class="text-2xl font-bold text-white mb-1" id="kpiTotalPenerima">0</div>
                <p class="text-[11px] text-slate-400">Subjek terdaftar</p>
            </div>

            <!-- Total Luas & Rata-rata -->
            <div class="glass-card p-5 rounded-2xl border border-slate-800 relative overflow-hidden">
                <div class="flex items-center justify-between mb-2">
                    <span class="text-xs text-slate-400 font-medium">Total Luas Tanah</span>
                    <span class="w-8 h-8 rounded-lg bg-amber-500/20 text-amber-400 flex items-center justify-center text-sm">
                        <i class="fa-solid fa-vector-square"></i>
                    </span>
                </div>
                <div class="text-2xl font-bold text-white mb-1" id="kpiTotalLuas">0 m²</div>
                <p class="text-[11px] text-slate-400" id="kpiRataLuas">Rata-rata: 0 m² / subjek</p>
            </div>

            <!-- Total Penghasilan Th 1 -->
            <div class="glass-card p-5 rounded-2xl border border-slate-800 relative overflow-hidden">
                <div class="flex items-center justify-between mb-2">
                    <span class="text-xs text-slate-400 font-medium">Total Penghasilan Th 1</span>
                    <span class="w-8 h-8 rounded-lg bg-teal-500/20 text-teal-400 flex items-center justify-center text-sm">
                        <i class="fa-solid fa-sack-dollar"></i>
                    </span>
                </div>
                <div class="text-2xl font-bold text-white mb-1" id="kpiTotalPenghasilan">Rp 0</div>
                <p class="text-[11px] text-slate-400" id="kpiRataPenghasilan">Rata: Rp 0 | Median: Rp 0</p>
            </div>

            <!-- Kelompok KUB & Pendampingan -->
            <div class="glass-card p-5 rounded-2xl border border-slate-800 relative overflow-hidden">
                <div class="flex items-center justify-between mb-2">
                    <span class="text-xs text-slate-400 font-medium">KUB & Pendampingan</span>
                    <span class="w-8 h-8 rounded-lg bg-purple-500/20 text-purple-400 flex items-center justify-center text-sm">
                        <i class="fa-solid fa-handshake"></i>
                    </span>
                </div>
                <div class="text-2xl font-bold text-white mb-1" id="kpiTotalKub">0 KUB</div>
                <p class="text-[11px] text-slate-400" id="kpiPendampinganPct">Pendampingan: 0% lengkap</p>
            </div>
        </section>

        <!-- Analytics Charts Grid 1 -->
        <section class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6">
            <!-- Chart 1: Asal Sertipikat -->
            <div class="glass-card p-5 rounded-2xl border border-slate-800">
                <h3 class="text-xs font-bold text-slate-200 uppercase tracking-wider mb-3 flex items-center gap-2">
                    <i class="fa-solid fa-certificate text-teal-400"></i> Asal Sertipikat (%)
                </h3>
                <div class="relative h-72 w-full">
                    <canvas id="chartSertipikat"></canvas>
                </div>
            </div>

            <!-- Chart 2: Pemanfaatan Lahan -->
            <div class="glass-card p-5 rounded-2xl border border-slate-800">
                <h3 class="text-xs font-bold text-slate-200 uppercase tracking-wider mb-3 flex items-center gap-2">
                    <i class="fa-solid fa-mountain-sun text-emerald-400"></i> Pemanfaatan Lahan (%)
                </h3>
                <div class="relative h-72 w-full">
                    <canvas id="chartPemanfaatan"></canvas>
                </div>
            </div>

            <!-- Chart 3: Jenis Usaha -->
            <div class="glass-card p-5 rounded-2xl border border-slate-800">
                <h3 class="text-xs font-bold text-slate-200 uppercase tracking-wider mb-3 flex items-center gap-2">
                    <i class="fa-solid fa-briefcase text-sky-400"></i> Jenis Usaha (%)
                </h3>
                <div class="relative h-72 w-full">
                    <canvas id="chartJenisUsaha"></canvas>
                </div>
            </div>

            <!-- Chart Model Pemberdayaan -->
            <div class="glass-card p-5 rounded-2xl border border-slate-800">
                <h3 class="text-xs font-bold text-slate-200 uppercase tracking-wider mb-3 flex items-center gap-2">
                    <i class="fa-solid fa-network-wired text-purple-400"></i> Model Pemberdayaan (%)
                </h3>
                <div class="relative h-72 w-full">
                    <canvas id="chartModelPemberdayaan"></canvas>
                </div>
            </div>
        </section>

        <!-- Analytics Charts Grid 2 -->
        <section class="grid grid-cols-1 md:grid-cols-3 gap-6">
            <!-- Chart 4: Akses yang Dibutuhkan -->
            <div class="glass-card p-5 rounded-2xl border border-slate-800">
                <h3 class="text-xs font-bold text-slate-200 uppercase tracking-wider mb-3 flex items-center gap-2">
                    <i class="fa-solid fa-hand-holding-hand text-indigo-400"></i> Akses Dibutuhkan (%)
                </h3>
                <div class="relative h-72 w-full">
                    <canvas id="chartAkses"></canvas>
                </div>
            </div>

            <!-- Chart 5: Kendala Utama / HKM -->
            <div class="glass-card p-5 rounded-2xl border border-slate-800">
                <h3 class="text-xs font-bold text-slate-200 uppercase tracking-wider mb-3 flex items-center gap-2">
                    <i class="fa-solid fa-circle-exclamation text-pink-400"></i> Kendala Utama (HKM) (%)
                </h3>
                <div class="relative h-72 w-full">
                    <canvas id="chartHkm"></canvas>
                </div>
            </div>

            <!-- Chart 6: Klasifikasi Luas Tanah -->
            <div class="glass-card p-5 rounded-2xl border border-slate-800">
                <h3 class="text-xs font-bold text-slate-200 uppercase tracking-wider mb-3 flex items-center gap-2">
                    <i class="fa-solid fa-ruler-combined text-amber-400"></i> Klasifikasi Luas Tanah (%)
                </h3>
                <div class="relative h-72 w-full">
                    <canvas id="chartKelasLuas"></canvas>
                </div>
            </div>
        </section>

        <section class="grid grid-cols-1 lg:grid-cols-2 gap-6">
            <!-- Chart 7: Penerima per Wilayah -->
            <div class="glass-card p-5 rounded-2xl border border-slate-800">
                <h3 class="text-xs font-bold text-slate-200 uppercase tracking-wider mb-3 flex items-center gap-2">
                    <i class="fa-solid fa-chart-column text-emerald-400"></i> Sebaran Penerima per Desa / Banjar
                </h3>
                <div class="relative h-72 w-full">
                    <canvas id="chartWilayah"></canvas>
                </div>
            </div>

            <!-- Chart 8: Top Komoditas Usaha -->
            <div class="glass-card p-5 rounded-2xl border border-slate-800">
                <h3 class="text-xs font-bold text-slate-200 uppercase tracking-wider mb-3 flex items-center gap-2">
                    <i class="fa-solid fa-wheat-awn text-sky-400"></i> Top Komoditas Usaha
                </h3>
                <div class="relative h-72 w-full">
                    <canvas id="chartKomoditas"></canvas>
                </div>
            </div>
        </section>

        <!-- Data Table Section -->
        <section class="glass-card rounded-2xl border border-slate-800 overflow-hidden">
            <div class="p-5 border-b border-slate-800 flex flex-col md:flex-row md:items-center justify-between gap-3">
                <div>
                    <h3 class="text-xs font-bold text-slate-200 uppercase tracking-wider flex items-center gap-2">
                        <i class="fa-solid fa-table text-emerald-400"></i> Rincian Data Penerima
                    </h3>
                    <p class="text-xs text-slate-400">Daftar terfilter berdasarkan kriteria penyaringan di atas</p>
                </div>
                <button onclick="exportToCSV()" class="px-3.5 py-1.5 bg-slate-800 hover:bg-slate-700 text-xs text-slate-200 border border-slate-700 rounded-xl transition flex items-center gap-2 self-start md:self-auto">
                    <i class="fa-solid fa-download text-emerald-400"></i> Unduh Data Filter (CSV)
                </button>
            </div>

            <div class="overflow-x-auto custom-scrollbar">
                <table class="w-full text-left text-xs border-collapse">
                    <thead>
                        <tr class="bg-slate-800/80 text-slate-400 uppercase tracking-wider border-b border-slate-800">
                            <th class="p-3.5 font-semibold">No</th>
                            <th class="p-3.5 font-semibold">Kabupaten</th>
                            <th class="p-3.5 font-semibold">NIK & Nama</th>
                            <th class="p-3.5 font-semibold">Wilayah (Kec/Desa)</th>
                            <th class="p-3.5 font-semibold">No SHAT / Skema</th>
                            <th class="p-3.5 font-semibold">Luas (m²)</th>
                            <th class="p-3.5 font-semibold">Komoditas / Usaha</th>
                            <th class="p-3.5 font-semibold">Model Pemberdayaan</th>
                            <th class="p-3.5 font-semibold">Penghasilan Th 1</th>
                            <th class="p-3.5 font-semibold">Akses / Pendamping</th>
                        </tr>
                    </thead>
                    <tbody id="dataTableBody" class="divide-y divide-slate-800/50 text-slate-300">
                        <!-- Dynamic Rows -->
                    </tbody>
                </table>
            </div>

            <!-- Table Pagination -->
            <div class="p-4 border-t border-slate-800 flex items-center justify-between text-xs text-slate-400">
                <span id="pageInfo">Menampilkan 0 - 0 data</span>
                <div class="flex items-center gap-2">
                    <button id="btnPrevPage" onclick="changePage(-1)" class="px-3 py-1 bg-slate-800 hover:bg-slate-700 rounded-lg border border-slate-700 disabled:opacity-50">
                        <i class="fa-solid fa-chevron-left"></i>
                    </button>
                    <span id="currentPageNum" class="font-semibold text-slate-200">1</span>
                    <button id="btnNextPage" onclick="changePage(1)" class="px-3 py-1 bg-slate-800 hover:bg-slate-700 rounded-lg border border-slate-700 disabled:opacity-50">
                        <i class="fa-solid fa-chevron-right"></i>
                    </button>
                </div>
            </div>
        </section>

    </main>

    <script>
        // URL Web App Google Apps Script Anda
        const GAS_API_URL = "https://script.google.com/macros/s/AKfycby5f3NBOkwiF74C6BM0WpfnMdzUoujtHyAD1HHMfx8xvHqS6svKsK9jslqgXZ1sUoDjow/exec";

        // Global State Management
        let rawDataset = [];
        let filteredDataset = [];
        let currentPage = 1;
        const rowsPerPage = 7;
        
        // Auto-refresh timer
        let refreshIntervalTimer = null;
        let secondsRemaining = 300; // 5 menit

        // Chart Instances
        let chartWilayahInstance = null;
        let chartSertipikatInstance = null;
        let chartPemanfaatanInstance = null;
        let chartJenisUsahaInstance = null;
        let chartModelPemberdayaanInstance = null;
        let chartAksesInstance = null;
        let chartHkmInstance = null;
        let chartKelasLuasInstance = null;
        let chartKomoditasInstance = null;

        // Sample Fallback Dataset (bila API terjadi masalah)
        const SAMPLE_FALLBACK = [
            { id: "1", sumberTab: "Tabulasi Badung", nik: "5103010101900001", nama: "I Wayan Sudiarta", kabupaten: "Kabupaten Badung", kecamatan: "Petang", desa: "Pelaga", alamat: "Br. Telanga", nomorShat: "01234", jenisHak: "Hak Milik", tahunShat: 2021, asalSertipikat: "PTSL", luas: 1250, pemanfaatan: "Perkebunan", jenisUsaha: "Pertanian", namaUsaha: "Kopi, Hortikultura", komoditasList: ["Kopi", "Hortikultura"], jenisKub: "KUB Tani Mandiri", hkm: "Modal Usaha", akses: "Kredit Usaha Rakyat (KUR)", modelPemberdayaan: "Pendampingan Usaha", instansi: "Dinas Pertanian", jenisPendampingan: "Pelatihan", namaKegiatan: "Bimtek Organik", penghasilan: 36000000 },
            { id: "2", sumberTab: "Tabulasi Badung", nik: "5103010101900002", nama: "I Made Rai", kabupaten: "Kabupaten Badung", kecamatan: "Petang", desa: "Pelaga", alamat: "Br. Karang Gadong", nomorShat: "01235", jenisHak: "Hak Milik", tahunShat: 2022, asalSertipikat: "PRONA", luas: 850, pemanfaatan: "Sawah", jenisUsaha: "Pertanian", namaUsaha: "Padi Organik", komoditasList: ["Padi Organik"], jenisKub: "KUB Padi Bersama", hkm: "Pemasaran", akses: "Bantuan Bibit", modelPemberdayaan: "Bantuan Sarana/Prasarana", instansi: "Dinas Pertanian", jenisPendampingan: "Bibit", namaKegiatan: "Penyuluhan", penghasilan: 28000000 }
        ];

        window.addEventListener('DOMContentLoaded', () => {
            loadDashboardData(false);
            startAutoRefreshTimer();
        });

        async function loadDashboardData(forceRefresh) {
            const refreshIcon = document.getElementById('refreshIcon');
            refreshIcon.classList.add('fa-spin');
            
            // Jika berjalan di lingkungan Google Apps Script Web App
            if (typeof google !== 'undefined' && google.script && google.script.run) {
                google.script.run
                    .withSuccessHandler(onDataLoaded)
                    .withFailureHandler(onDataError)
                    .getDashboardData(forceRefresh);
            } 
            // Jika berjalan di Vercel/Hosting Publik (Memakai HTTP Fetch)
            else if (GAS_API_URL && !GAS_API_URL.includes("ISI_DENGAN_URL")) {
                try {
                    const fetchUrl = forceRefresh ? `${GAS_API_URL}?refresh=true` : GAS_API_URL;
                    const response = await fetch(fetchUrl);
                    const data = await response.json();
                    onDataLoaded(data);
                } catch (err) {
                    console.error("Fetch Error:", err);
                    onDataError("Gagal mengambil data dari Google Sheet API.");
                }
            } 
            // Fallback Data Sampel
            else {
                setTimeout(() => {
                    onDataLoaded({
                        error: false,
                        timestamp: new Date().toLocaleString('id-ID'),
                        tabsProcessed: ["Tabulasi Badung", "Tabulasi Buleleng", "Tabulasi Gianyar"],
                        data: SAMPLE_FALLBACK
                    });
                }, 600);
            }
        }

        function onDataLoaded(response) {
            document.getElementById('refreshIcon').classList.remove('fa-spin');
            secondsRemaining = 300;

            if (response.error) {
                alert("Error: " + response.message);
                return;
            }

            rawDataset = response.data || [];
            document.getElementById('lastUpdateText').textContent = `Terakhir diperbarui: ${response.timestamp}`;

            const tabs = response.tabsProcessed || [];
            document.getElementById('tabListBadge').textContent = tabs.join(', ') || 'Tabulasi';

            updateCascadingDropdowns();
            applyFilters();
        }

        function onDataError(err) {
            document.getElementById('refreshIcon').classList.remove('fa-spin');
            console.error(err);
            alert("Gagal memuat data: " + err);
        }

        function startAutoRefreshTimer() {
            if (refreshIntervalTimer) clearInterval(refreshIntervalTimer);
            refreshIntervalTimer = setInterval(() => {
                secondsRemaining--;
                if (secondsRemaining <= 0) {
                    loadDashboardData(true);
                } else {
                    const mins = String(Math.floor(secondsRemaining / 60)).padStart(2, '0');
                    const secs = String(secondsRemaining % 60).padStart(2, '0');
                    document.getElementById('timerText').textContent = `${mins}:${secs}`;
                }
            }, 1000);
        }

        function getActiveFilterValues() {
            return {
                tab: document.getElementById('filterTab').value,
                kab: document.getElementById('filterKabupaten').value,
                kec: document.getElementById('filterKecamatan').value,
                desa: document.getElementById('filterDesa').value,
                jUsaha: document.getElementById('filterJenisUsaha').value,
                jKub: document.getElementById('filterJenisKub').value,
                komoditas: document.getElementById('filterKomoditas').value,
                sertipikat: document.getElementById('filterSertipikat').value,
                hkm: document.getElementById('filterHkm').value,
                modelPemberdayaan: document.getElementById('filterModelPemberdayaan').value,
                search: document.getElementById('searchInput').value.toLowerCase().trim()
            };
        }

        function onFilterChange(changedFilterId) {
            updateCascadingDropdowns(changedFilterId);
            applyFilters();
        }

        function updateCascadingDropdowns(triggerId = '') {
            const current = getActiveFilterValues();

            const getSubsetExcept = (ignoreKeys = []) => {
                return rawDataset.filter(item => {
                    const matchTab = ignoreKeys.includes('tab') || current.tab === 'ALL' || item.sumberTab === current.tab;
                    const matchKab = ignoreKeys.includes('kab') || current.kab === 'ALL' || item.kabupaten === current.kab;
                    const matchKec = ignoreKeys.includes('kec') || current.kec === 'ALL' || item.kecamatan === current.kec;
                    const matchDesa = ignoreKeys.includes('desa') || current.desa === 'ALL' || item.desa === current.desa;
                    const matchUsaha = ignoreKeys.includes('jUsaha') || current.jUsaha === 'ALL' || item.jenisUsaha === current.jUsaha;
                    const matchKub = ignoreKeys.includes('jKub') || current.jKub === 'ALL' || item.jenisKub === current.jKub;
                    const matchKom = ignoreKeys.includes('komoditas') || current.komoditas === 'ALL' || (item.komoditasList && item.komoditasList.includes(current.komoditas));
                    const matchSert = ignoreKeys.includes('sertipikat') || current.sertipikat === 'ALL' || item.asalSertipikat === current.sertipikat;
                    const matchHkm = ignoreKeys.includes('hkm') || current.hkm === 'ALL' || item.hkm === current.hkm;
                    const matchModel = ignoreKeys.includes('modelPemberdayaan') || current.modelPemberdayaan === 'ALL' || item.modelPemberdayaan === current.modelPemberdayaan;

                    return matchTab && matchKab && matchKec && matchDesa && matchUsaha && matchKub && matchKom && matchSert && matchHkm && matchModel;
                });
            };

            const populateDropdown = (elemId, subset, fieldKey, currentVal, labelDefault, isArray = false) => {
                const select = document.getElementById(elemId);
                let valuesSet = new Set();

                subset.forEach(d => {
                    if (isArray) {
                        (d[fieldKey] || []).forEach(v => { if (v && v !== '-') valuesSet.add(v); });
                    } else {
                        if (d[fieldKey] && d[fieldKey] !== '-') valuesSet.add(d[fieldKey]);
                    }
                });

                const sorted = Array.from(valuesSet).sort();

                let html = `<option value="ALL">Semua ${labelDefault}</option>`;
                sorted.forEach(val => {
                    html += `<option value="${val}">${val}</option>`;
                });

                select.innerHTML = html;

                if (sorted.includes(currentVal)) {
                    select.value = currentVal;
                } else {
                    select.value = 'ALL';
                }
            };

            populateDropdown('filterTab', getSubsetExcept(['tab']), 'sumberTab', current.tab, 'Tab');
            populateDropdown('filterKabupaten', getSubsetExcept(['kab']), 'kabupaten', current.kab, 'Kabupaten');
            populateDropdown('filterKecamatan', getSubsetExcept(['kec']), 'kecamatan', current.kec, 'Kecamatan');
            populateDropdown('filterDesa', getSubsetExcept(['desa']), 'desa', current.desa, 'Desa');
            populateDropdown('filterJenisUsaha', getSubsetExcept(['jUsaha']), 'jenisUsaha', current.jUsaha, 'Jenis Usaha');
            populateDropdown('filterJenisKub', getSubsetExcept(['jKub']), 'jenisKub', current.jKub, 'KUB');
            populateDropdown('filterKomoditas', getSubsetExcept(['komoditas']), 'komoditasList', current.komoditas, 'Komoditas', true);
            populateDropdown('filterModelPemberdayaan', getSubsetExcept(['modelPemberdayaan']), 'modelPemberdayaan', current.modelPemberdayaan, 'Model');
            populateDropdown('filterSertipikat', getSubsetExcept(['sertipikat']), 'asalSertipikat', current.sertipikat, 'Skema');
            populateDropdown('filterHkm', getSubsetExcept(['hkm']), 'hkm', current.hkm, 'Kendala');
        }

        function resetFilters() {
            document.querySelectorAll('select').forEach(s => s.value = 'ALL');
            document.getElementById('searchInput').value = '';
            updateCascadingDropdowns();
            applyFilters();
        }

        function applyFilters() {
            const current = getActiveFilterValues();

            filteredDataset = rawDataset.filter(item => {
                const matchTab = (current.tab === 'ALL' || item.sumberTab === current.tab);
                const matchKab = (current.kab === 'ALL' || item.kabupaten === current.kab);
                const matchKec = (current.kec === 'ALL' || item.kecamatan === current.kec);
                const matchDesa = (current.desa === 'ALL' || item.desa === current.desa);
                const matchUsaha = (current.jUsaha === 'ALL' || item.jenisUsaha === current.jUsaha);
                const matchKub = (current.jKub === 'ALL' || item.jenisKub === current.jKub);
                const matchKom = (current.komoditas === 'ALL' || (item.komoditasList && item.komoditasList.includes(current.komoditas)));
                const matchSert = (current.sertipikat === 'ALL' || item.asalSertipikat === current.sertipikat);
                const matchHkm = (current.hkm === 'ALL' || item.hkm === current.hkm);
                const matchModel = (current.modelPemberdayaan === 'ALL' || item.modelPemberdayaan === current.modelPemberdayaan);
                
                const matchSearch = !current.search || 
                    (item.nama && item.nama.toLowerCase().includes(current.search)) || 
                    (item.nik && item.nik.includes(current.search)) ||
                    (item.alamat && item.alamat.toLowerCase().includes(current.search)) ||
                    (item.namaUsaha && item.namaUsaha.toLowerCase().includes(current.search));

                return matchTab && matchKab && matchKec && matchDesa && matchUsaha && matchKub && matchKom && matchSert && matchHkm && matchModel && matchSearch;
            });

            currentPage = 1;
            updateKPIs();
            renderCharts();
            renderTable();
        }

        function updateKPIs() {
            const total = filteredDataset.length;
            document.getElementById('kpiTotalPenerima').textContent = total.toLocaleString('id-ID');
            document.getElementById('recordCountText').textContent = `${total} Data Terfilter`;

            const totalLuas = filteredDataset.reduce((sum, d) => sum + (d.luas || 0), 0);
            const rataLuas = total > 0 ? (totalLuas / total).toFixed(1) : 0;
            document.getElementById('kpiTotalLuas').textContent = `${totalLuas.toLocaleString('id-ID')} m²`;
            document.getElementById('kpiRataLuas').textContent = `Rata-rata: ${rataLuas} m² / subjek`;

            const totalPenghasilan = filteredDataset.reduce((sum, d) => sum + (d.penghasilan || 0), 0);
            const rataPenghasilan = total > 0 ? totalPenghasilan / total : 0;
            
            const sortedIncomes = filteredDataset.map(d => d.penghasilan || 0).sort((a,b) => a - b);
            let medianIncome = 0;
            if (total > 0) {
                const mid = Math.floor(total / 2);
                medianIncome = total % 2 !== 0 ? sortedIncomes[mid] : (sortedIncomes[mid - 1] + sortedIncomes[mid]) / 2;
            }

            document.getElementById('kpiTotalPenghasilan').textContent = `Rp ${formatJuta(totalPenghasilan)}`;
            document.getElementById('kpiRataPenghasilan').textContent = `Rata: Rp ${formatJuta(rataPenghasilan)} | Median: Rp ${formatJuta(medianIncome)}`;

            const totalKub = new Set(filteredDataset.map(d => d.jenisKub).filter(k => k && k !== '-')).size;
            const lengkapPendamping = filteredDataset.filter(d => d.instansi && d.jenisPendampingan).length;
            const pctPendamping = total > 0 ? Math.round((lengkapPendamping / total) * 100) : 0;

            document.getElementById('kpiTotalKub').textContent = `${totalKub} Kelompok KUB`;
            document.getElementById('kpiPendampinganPct').textContent = `Pendampingan: ${pctPendamping}% terdata`;
        }

        function formatJuta(val) {
            if (val >= 1000000000) return (val / 1000000000).toFixed(1) + " M";
            if (val >= 1000000) return (val / 1000000).toFixed(1) + " Jt";
            return val.toLocaleString('id-ID');
        }

        function renderCharts() {
            // 1. Asal Sertipikat (%)
            const sertCounts = {};
            filteredDataset.forEach(d => {
                const key = d.asalSertipikat || "Tidak Ada Data";
                sertCounts[key] = (sertCounts[key] || 0) + 1;
            });
            createDoughnutWithPercentageChart('chartSertipikat', Object.keys(sertCounts), Object.values(sertCounts), chartSertipikatInstance, inst => chartSertipikatInstance = inst);

            // 2. Pemanfaatan Lahan (%)
            const pemanfaatanCounts = {};
            filteredDataset.forEach(d => {
                const key = d.pemanfaatan || "Lainnya";
                pemanfaatanCounts[key] = (pemanfaatanCounts[key] || 0) + 1;
            });
            createDoughnutWithPercentageChart('chartPemanfaatan', Object.keys(pemanfaatanCounts), Object.values(pemanfaatanCounts), chartPemanfaatanInstance, inst => chartPemanfaatanInstance = inst);

            // 3. Jenis Usaha (%)
            const jenisUsahaCounts = {};
            filteredDataset.forEach(d => {
                const key = d.jenisUsaha || "Belum Ada Usaha";
                jenisUsahaCounts[key] = (jenisUsahaCounts[key] || 0) + 1;
            });
            createDoughnutWithPercentageChart('chartJenisUsaha', Object.keys(jenisUsahaCounts), Object.values(jenisUsahaCounts), chartJenisUsahaInstance, inst => chartJenisUsahaInstance = inst);

            // 4. Model Pemberdayaan (%)
            const modelCounts = {};
            filteredDataset.forEach(d => {
                const key = d.modelPemberdayaan || "Belum Ada Model";
                modelCounts[key] = (modelCounts[key] || 0) + 1;
            });
            createDoughnutWithPercentageChart('chartModelPemberdayaan', Object.keys(modelCounts), Object.values(modelCounts), chartModelPemberdayaanInstance, inst => chartModelPemberdayaanInstance = inst);

            // 5. Akses yang Dibutuhkan (%)
            const aksesCounts = {};
            filteredDataset.forEach(d => {
                const key = d.akses || "Belum Ada Akses";
                aksesCounts[key] = (aksesCounts[key] || 0) + 1;
            });
            createDoughnutWithPercentageChart('chartAkses', Object.keys(aksesCounts), Object.values(aksesCounts), chartAksesInstance, inst => chartAksesInstance = inst);

            // 6. Kendala Utama (HKM) (%)
            const hkmCounts = {};
            filteredDataset.forEach(d => {
                const key = d.hkm || "Tidak Ada Kendala";
                hkmCounts[key] = (hkmCounts[key] || 0) + 1;
            });
            createDoughnutWithPercentageChart('chartHkm', Object.keys(hkmCounts), Object.values(hkmCounts), chartHkmInstance, inst => chartHkmInstance = inst);

            // 7. Klasifikasi Luas Tanah (%)
            const luasBuckets = { '<500 m²': 0, '500-1000 m²': 0, '1000-5000 m²': 0, '>5000 m²': 0 };
            filteredDataset.forEach(d => {
                const l = d.luas || 0;
                if (l < 500) luasBuckets['<500 m²']++;
                else if (l <= 1000) luasBuckets['500-1000 m²']++;
                else if (l <= 5000) luasBuckets['1000-5000 m²']++;
                else luasBuckets['>5000 m²']++;
            });
            createDoughnutWithPercentageChart('chartKelasLuas', Object.keys(luasBuckets), Object.values(luasBuckets), chartKelasLuasInstance, inst => chartKelasLuasInstance = inst);

            // 8. Sebaran Penerima per Desa / Banjar
            const wilCounts = {};
            filteredDataset.forEach(d => {
                const key = d.desa || d.kecamatan || "Lainnya";
                wilCounts[key] = (wilCounts[key] || 0) + 1;
            });
            createBarChart('chartWilayah', Object.keys(wilCounts), Object.values(wilCounts), '#10b981', chartWilayahInstance, inst => chartWilayahInstance = inst);

            // 9. Top Komoditas Usaha
            const komCounts = {};
            filteredDataset.forEach(d => {
                (d.komoditasList || []).forEach(k => {
                    if (k && k !== '-') komCounts[k] = (komCounts[k] || 0) + 1;
                });
            });
            const topKom = Object.entries(komCounts).sort((a,b) => b[1] - a[1]).slice(0, 5);
            createBarChart('chartKomoditas', topKom.map(t => t[0]), topKom.map(t => t[1]), '#0284c7', chartKomoditasInstance, inst => chartKomoditasInstance = inst, 'y');
        }

        function createDoughnutWithPercentageChart(canvasId, rawLabels, data, currentInstance, setInstance) {
            if (currentInstance) currentInstance.destroy();
            const ctx = document.getElementById(canvasId).getContext('2d');

            const colorPalette = [
                '#10b981', '#06b6d4', '#f59e0b', '#ec4899', 
                '#8b5cf6', '#3b82f6', '#f43f5e', '#14b8a6', '#a855f7'
            ];

            const grandTotal = data.reduce((a, b) => a + b, 0);

            const labelsWithPct = rawLabels.map((label, index) => {
                const count = data[index] || 0;
                const pct = grandTotal > 0 ? ((count / grandTotal) * 100).toFixed(1) : "0.0";
                return `${label} (${pct}%)`;
            });

            const newInst = new Chart(ctx, {
                type: 'doughnut',
                data: {
                    labels: labelsWithPct,
                    datasets: [{
                        data: data,
                        backgroundColor: colorPalette.slice(0, rawLabels.length),
                        borderWidth: 2,
                        borderColor: '#1e293b'
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: { 
                            position: 'bottom', 
                            labels: { 
                                color: '#cbd5e1', 
                                font: { size: 10 },
                                boxWidth: 12,
                                padding: 12
                            } 
                        },
                        tooltip: {
                            callbacks: {
                                label: function(context) {
                                    const val = context.raw || 0;
                                    const pct = grandTotal > 0 ? ((val / grandTotal) * 100).toFixed(1) : "0.0";
                                    return ` ${context.label.split('(')[0].trim()}: ${val} subjek (${pct}%)`;
                                }
                            }
                        }
                    }
                }
            });
            setInstance(newInst);
        }

        function createBarChart(canvasId, labels, data, color, currentInstance, setInstance, indexAxis = 'x') {
            if (currentInstance) currentInstance.destroy();
            const ctx = document.getElementById(canvasId).getContext('2d');
            const newInst = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: labels,
                    datasets: [{ data: data, backgroundColor: color, borderRadius: 6 }]
                },
                options: {
                    indexAxis: indexAxis,
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: { legend: { display: false } },
                    scales: {
                        x: { ticks: { color: '#94a3b8', font: { size: 10 } }, grid: { display: false } },
                        y: { ticks: { color: '#94a3b8', font: { size: 10 } }, grid: { color: 'rgba(255,255,255,0.05)' } }
                    }
                }
            });
            setInstance(newInst);
        }

        function renderTable() {
            const tbody = document.getElementById('dataTableBody');
            tbody.innerHTML = '';

            const total = filteredDataset.length;
            const totalPages = Math.ceil(total / rowsPerPage) || 1;
            if (currentPage > totalPages) currentPage = totalPages;

            const startIdx = (currentPage - 1) * rowsPerPage;
            const endIdx = Math.min(startIdx + rowsPerPage, total);
            const pageItems = filteredDataset.slice(startIdx, endIdx);

            if (pageItems.length === 0) {
                tbody.innerHTML = `<tr><td colspan="10" class="p-6 text-center text-slate-500">Tidak ada data yang sesuai dengan kriteria filter.</td></tr>`;
            } else {
                pageItems.forEach((item, idx) => {
                    const tr = document.createElement('tr');
                    tr.className = "hover:bg-slate-800/40 transition";
                    tr.innerHTML = `
                        <td class="p-3.5 text-slate-500 font-mono">${startIdx + idx + 1}</td>
                        <td class="p-3.5"><span class="px-2 py-0.5 rounded text-[10px] bg-slate-800 border border-slate-700 text-slate-300">${item.kabupaten || '-'}</span></td>
                        <td class="p-3.5">
                            <div class="font-semibold text-slate-200">${item.nama}</div>
                            <div class="text-[10px] text-slate-400 font-mono">${item.nik}</div>
                        </td>
                        <td class="p-3.5">
                            <div>${item.desa}</div>
                            <div class="text-[10px] text-slate-500">${item.kecamatan}</div>
                        </td>
                        <td class="p-3.5">
                            <div>${item.nomorShat || '-'}</div>
                            <span class="text-[10px] text-emerald-400 font-medium">${item.asalSertipikat}</span>
                        </td>
                        <td class="p-3.5 font-mono text-amber-400">${item.luas} m²</td>
                        <td class="p-3.5">
                            <div class="text-slate-200">${item.namaUsaha || '-'}</div>
                            <div class="text-[10px] text-slate-400">${item.jenisUsaha}</div>
                        </td>
                        <td class="p-3.5">
                            <span class="px-2 py-0.5 rounded text-[10px] bg-purple-500/20 text-purple-300 border border-purple-500/30">${item.modelPemberdayaan || '-'}</span>
                        </td>
                        <td class="p-3.5 font-semibold text-teal-400">Rp ${item.penghasilan.toLocaleString('id-ID')}</td>
                        <td class="p-3.5">
                            <div class="text-[11px] text-slate-300">${item.akses || '-'}</div>
                            <div class="text-[10px] text-purple-400">${item.instansi || '-'}</div>
                        </td>
                    `;
                    tbody.appendChild(tr);
                });
            }

            document.getElementById('pageInfo').textContent = total > 0 ? `Menampilkan ${startIdx + 1} - ${endIdx} dari ${total} data` : 'Menampilkan 0 data';
            document.getElementById('currentPageNum').textContent = `${currentPage} / ${totalPages}`;
            document.getElementById('btnPrevPage').disabled = (currentPage === 1);
            document.getElementById('btnNextPage').disabled = (currentPage >= totalPages);
        }

        function changePage(delta) {
            currentPage += delta;
            renderTable();
        }

        function exportToCSV() {
            if (filteredDataset.length === 0) return;
            const headers = ["Kabupaten", "Kecamatan", "Desa", "NIK", "Nama", "Nomor SHAT", "Asal Sertipikat", "Luas m2", "Pemanfaatan Lahan", "Jenis Usaha", "Nama Usaha", "Model Pemberdayaan", "Akses Dibutuhkan", "Penghasilan Th 1"];
            const rows = filteredDataset.map(d => [
                `"${d.kabupaten}"`, `"${d.kecamatan}"`, `"${d.desa}"`, `"${d.nik}"`, `"${d.nama}"`, `"${d.nomorShat}"`, `"${d.asalSertipikat}"`, d.luas, `"${d.pemanfaatan}"`, `"${d.jenisUsaha}"`, `"${d.namaUsaha}"`, `"${d.modelPemberdayaan}"`, `"${d.akses}"`, d.penghasilan
            ]);

            const csvContent = "data:text/csv;charset=utf-8," + [headers.join(","), ...rows.map(e => e.join(","))].join("\n");
            const encodedUri = encodeURI(csvContent);
            const link = document.createElement("a");
            link.setAttribute("href", encodedUri);
            link.setAttribute("download", `Data_Agraria_Export_${new Date().toISOString().slice(0,10)}.csv`);
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
        }
    </script>
</body>
</html>