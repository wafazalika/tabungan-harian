
<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Aplikasi Catatan Tabungan Harian</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        .fade-in {
            animation: fadeIn 0.5s;
        }
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }
    </style>
</head>
<body class="bg-gray-100 min-h-screen">
    <div class="container mx-auto px-4 py-8 max-w-4xl">
        <!-- Header -->
        <header class="bg-gradient-to-r from-blue-600 to-blue-400 text-white rounded-xl shadow-lg p-6 mb-8">
            <div class="flex items-center justify-between">
                <div>
                    <h1 class="text-3xl font-bold">
                        <i class="fas fa-piggy-bank mr-3"></i>Tabungan Harian
                    </h1>
                    <p class="mt-2">Catat setiap tabungan harian Anda untuk mencapai tujuan finansial</p>
                </div>
                <div class="bg-white text-blue-600 rounded-full p-4 shadow-md">
                    <i class="fas fa-wallet text-3xl"></i>
                </div>
            </div>
        </header>

        <!-- Form Input Tabungan -->
        <div class="bg-white rounded-xl shadow-md p-6 mb-8">
            <h2 class="text-xl font-semibold text-gray-800 mb-4">
                <i class="fas fa-plus-circle mr-2 text-blue-500"></i>Tambah Tabungan
            </h2>
            <form id="tabunganForm" class="space-y-4">
                <div>
                    <label for="amount" class="block text-sm font-medium text-gray-700 mb-1">Jumlah (Rp)</label>
                    <div class="relative">
                        <span class="absolute inset-y-0 left-0 flex items-center pl-3 text-gray-500">Rp</span>
                        <input type="number" id="amount" min="0" step="500" 
                               class="w-full pl-10 pr-4 py-2 border border-gray-300 rounded-lg focus:ring-blue-500 focus:border-blue-500" 
                               placeholder="50000" required>
                    </div>
                </div>
                
                <div>
                    <label for="date" class="block text-sm font-medium text-gray-700 mb-1">Tanggal</label>
                    <input type="date" id="date" 
                           class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-blue-500 focus:border-blue-500" 
                           required>
                </div>
                
                <div>
                    <label for="note" class="block text-sm font-medium text-gray-700 mb-1">Catatan</label>
                    <textarea id="note" rows="2"
                              class="w-full px-4 py-2 border border-gray-300 rounded-lg focus:ring-blue-500 focus:border-blue-500" 
                              placeholder="Misal: Tabungan liburan, beli laptop, dll"></textarea>
                </div>
                
                <button type="submit" 
                        class="w-full bg-blue-600 hover:bg-blue-700 text-white font-medium py-2 px-4 rounded-lg transition duration-300 flex items-center justify-center">
                    <i class="fas fa-save mr-2"></i> Simpan Tabungan
                </button>
            </form>
        </div>

        <!-- Statistik Tabungan -->
        <div class="bg-white rounded-xl shadow-md p-6 mb-8">
            <h2 class="text-xl font-semibold text-gray-800 mb-6">
                <i class="fas fa-chart-line mr-2 text-blue-500"></i>Statistik Tabungan
            </h2>
            
            <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-6">
                <div class="bg-blue-50 border-l-4 border-blue-500 rounded-lg p-4">
                    <h3 class="text-sm font-medium text-gray-500 uppercase">Total Tabungan</h3>
                    <p id="totalTabungan" class="text-2xl font-bold text-blue-600">Rp 0</p>
                </div>
                
                <div class="bg-green-50 border-l-4 border-green-500 rounded-lg p-4">
                    <h3 class="text-sm font-medium text-gray-500 uppercase">Rata-rata Harian</h3>
                    <p id="rataTabungan" class="text-2xl font-bold text-green-600">Rp 0</p>
                </div>
                
                <div class="bg-purple-50 border-l-4 border-purple-500 rounded-lg p-4">
                    <h3 class="text-sm font-medium text-gray-500 uppercase">Hari Menabung</h3>
                    <p id="hariMenabung" class="text-2xl font-bold text-purple-600">0</p>
                </div>
            </div>
            
            <div class="h-64">
                <canvas id="tabunganChart"></canvas>
            </div>
        </div>

        <!-- Daftar Tabungan -->
        <div class="bg-white rounded-xl shadow-md p-6">
            <div class="flex justify-between items-center mb-4">
                <h2 class="text-xl font-semibold text-gray-800">
                    <i class="fas fa-list-ul mr-2 text-blue-500"></i>Riwayat Tabungan
                </h2>
                <div class="relative">
                    <button id="filterToggle" class="flex items-center text-blue-600 font-medium">
                        <i class="fas fa-filter mr-1"></i> Filter
                    </button>
                    <div id="filterDropdown" class="hidden absolute right-0 mt-2 w-48 bg-white rounded-md shadow-lg z-10 border border-gray-200">
                        <div class="p-2">
                            <label class="flex items-center px-2 py-1 text-sm text-gray-700 hover:bg-gray-100 rounded">
                                <input type="radio" name="filter" value="all" class="mr-2" checked> Semua
                            </label>
                            <label class="flex items-center px-2 py-1 text-sm text-gray-700 hover:bg-gray-100 rounded">
                                <input type="radio" name="filter" value="week" class="mr-2"> 7 Hari Terakhir
                            </label>
                            <label class="flex items-center px-2 py-1 text-sm text-gray-700 hover:bg-gray-100 rounded">
                                <input type="radio" name="filter" value="month" class="mr-2"> 30 Hari Terakhir
                            </label>
                        </div>
                    </div>
                </div>
            </div>
            
            <div id="tabunganList" class="space-y-3">
                <!-- Item tabungan akan ditambahkan secara dinamis -->
                <div class="text-center py-10 text-gray-400">
                    <i class="fas fa-piggy-bank text-4xl mb-2"></i>
                    <p>Belum ada catatan tabungan</p>
                </div>
            </div>
        </div>
    </div>

    <script>
        // Data tabungan akan disimpan di localStorage
        const TABUNGAN_KEY = 'tabunganData';
        const tabunganForm = document.getElementById('tabunganForm');
        const amountInput = document.getElementById('amount');
        const dateInput = document.getElementById('date');
        const noteInput = document.getElementById('note');
        const tabunganList = document.getElementById('tabunganList');
        const totalTabungan = document.getElementById('totalTabungan');
        const rataTabungan = document.getElementById('rataTabungan');
        const hariMenabung = document.getElementById('hariMenabung');
        const filterToggle = document.getElementById('filterToggle');
        const filterDropdown = document.getElementById('filterDropdown');
        let tabunganChart = null;

        // Set default date ke hari ini
        dateInput.valueAsDate = new Date();

        // Inisialisasi data tabungan
        let tabunganData = JSON.parse(localStorage.getItem(TABUNGAN_KEY)) || [];

        // Event listeners
        tabunganForm.addEventListener('submit', saveTabungan);
        filterToggle.addEventListener('click', toggleFilterDropdown);
        document.addEventListener('click', closeFilterDropdown);
        document.querySelectorAll('input[name="filter"]').forEach(radio => {
            radio.addEventListener('change', filterTabungan);
        });

        // Tampilkan data saat pertama kali di-load
        renderTabungan();

        // Fungsi untuk menyimpan tabungan baru
        function saveTabungan(e) {
            e.preventDefault();
            
            const amount = parseInt(amountInput.value);
            const date = dateInput.value;
            const note = noteInput.value;
            
            if (!amount || amount <= 0) {
                alert('Jumlah tabungan harus diisi dengan angka positif');
                return;
            }
            
            if (!date) {
                alert('Tanggal harus diisi');
                return;
            }
            
            const newTabungan = {
                id: Date.now(),
                amount,
                date,
                note: note || 'Tanpa catatan'
            };
            
            tabunganData.push(newTabungan);
            localStorage.setItem(TABUNGAN_KEY, JSON.stringify(tabunganData));
            
            // Reset form
            amountInput.value = '';
            noteInput.value = '';
            dateInput.valueAsDate = new Date();
            
            // Update tampilan
            renderTabungan();
            
            // Animasi
            const savedAlert = document.createElement('div');
            savedAlert.className = 'fixed top-4 right-4 bg-green-500 text-white px-4 py-2 rounded-lg shadow-lg fade-in';
            savedAlert.innerHTML = '<i class="fas fa-check-circle mr-2"></i> Tabungan berhasil disimpan!';
            document.body.appendChild(savedAlert);
            
            setTimeout(() => {
                savedAlert.classList.remove('fade-in');
                savedAlert.classList.add('fade-out');
                setTimeout(() => savedAlert.remove(), 500);
            }, 3000);
        }

        // Fungsi untuk menampilkan data tabungan
        function renderTabungan(filter = 'all') {
            let filteredData = [...tabunganData];
            
            if (filter === 'week') {
                const oneWeekAgo = new Date();
                oneWeekAgo.setDate(oneWeekAgo.getDate() - 7);
                filteredData = filteredData.filter(item => new Date(item.date) >= oneWeekAgo);
            } else if (filter === 'month') {
                const oneMonthAgo = new Date();
                oneMonthAgo.setMonth(oneMonthAgo.getMonth() - 1);
                filteredData = filteredData.filter(item => new Date(item.date) >= oneMonthAgo);
            }
            
            // Urutkan berdasarkan tanggal terbaru
            filteredData.sort((a, b) => new Date(b.date) - new Date(a.date));
            
            if (filteredData.length === 0) {
                tabunganList.innerHTML = `
                    <div class="text-center py-10 text-gray-400">
                        <i class="fas fa-piggy-bank text-4xl mb-2"></i>
                        <p>Belum ada catatan tabungan</p>
                    </div>
                `;
            } else {
                tabunganList.innerHTML = '';
                filteredData.forEach(item => {
                    const date = new Date(item.date);
                    const formattedDate = date.toLocaleDateString('id-ID', {
                        weekday: 'long',
                        day: 'numeric',
                        month: 'long',
                        year: 'numeric'
                    });
                    
                    const tabunganItem = document.createElement('div');
                    tabunganItem.className = 'bg-gray-50 rounded-lg p-4 border-l-4 border-blue-500 flex justify-between items-center fade-in';
                    tabunganItem.innerHTML = `
                        <div>
                            <h3 class="font-semibold text-blue-600">${item.note}</h3>
                            <p class="text-sm text-gray-500">${formattedDate}</p>
                        </div>
                        <div class="text-right">
                            <p class="font-bold text-green-600">+ Rp ${item.amount.toLocaleString('id-ID')}</p>
                            <button data-id="${item.id}" class="text-red-500 hover:text-red-700 text-sm delete-btn">
                                <i class="fas fa-trash-alt"></i>
                            </button>
                        </div>
                    `;
                    tabunganList.appendChild(tabunganItem);
                });
                
                // Tambahkan event listener untuk tombol delete
                document.querySelectorAll('.delete-btn').forEach(btn => {
                    btn.addEventListener('click', deleteTabungan);
                });
            }
            
            updateStatistics(filteredData);
            renderChart(filteredData);
        }

        // Fungsi untuk menghapus tabungan
        function deleteTabungan(e) {
            const idToDelete = parseInt(e.currentTarget.getAttribute('data-id'));
            
            if (confirm('Anda yakin ingin menghapus catatan tabungan ini?')) {
                tabunganData = tabunganData.filter(item => item.id !== idToDelete);
                localStorage.setItem(TABUNGAN_KEY, JSON.stringify(tabunganData));
                renderTabungan();
                
                // Animasi
                const deletedAlert = document.createElement('div');
                deletedAlert.className = 'fixed top-4 right-4 bg-red-500 text-white px-4 py-2 rounded-lg shadow-lg fade-in';
                deletedAlert.innerHTML = '<i class="fas fa-trash-alt mr-2"></i> Tabungan berhasil dihapus!';
                document.body.appendChild(deletedAlert);
                
                setTimeout(() => {
                    deletedAlert.classList.remove('fade-in');
                    deletedAlert.classList.add('fade-out');
                    setTimeout(() => deletedAlert.remove(), 500);
                }, 3000);
            }
        }

        // Fungsi untuk memperbarui statistik
        function updateStatistics(data) {
            const total = data.reduce((sum, item) => sum + item.amount, 0);
            const days = new Set(data.map(item => item.date)).size;
            const avg = days > 0 ? Math.round(total / days) : 0;
            
            totalTabungan.textContent = `Rp ${total.toLocaleString('id-ID')}`;
            rataTabungan.textContent = `Rp ${avg.toLocaleString('id-ID')}`;
            hariMenabung.textContent = data.length;
        }

        // Fungsi untuk menampilkan grafik
        function renderChart(data) {
            const ctx = document.getElementById('tabunganChart').getContext('2d');
            
            // Kelompokkan data per minggu
            const weeklyData = {};
            data.forEach(item => {
                const date = new Date(item.date);
                const year = date.getFullYear();
                const week = getWeekNumber(date);
                const key = `${year}-W${week}`;
                
                if (!weeklyData[key]) {
                    weeklyData[key] = {
                        label: `Minggu ${week}`,
                        total: 0,
                        date: firstDayOfWeek(date)
                    };
                }
                weeklyData[key].total += item.amount;
            });
            
            // Konversi ke array dan urutkan berdasarkan tanggal
            const chartData = Object.values(weeklyData).sort((a, b) => a.date - b.date);
            
            if (tabunganChart) {
                tabunganChart.destroy();
            }
            
            tabunganChart = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: chartData.map(item => item.label),
                    datasets: [{
                        label: 'Tabungan per Minggu',
                        data: chartData.map(item => item.total),
                        backgroundColor: 'rgba(54, 162, 235, 0.5)',
                        borderColor: 'rgba(54, 162, 235, 1)',
                        borderWidth: 1
                    }]
                },
                options: {
                    responsive: true,
                    scales: {
                        y: {
                            beginAtZero: true,
                            ticks: {
                                callback: function(value) {
                                    return 'Rp ' + value.toLocaleString('id-ID');
                                }
                            }
                        }
                    },
                    plugins: {
                        tooltip: {
                            callbacks: {
                                label: function(context) {
                                    return 'Total: Rp ' + context.raw.toLocaleString('id-ID');
                                }
                            }
                        }
                    }
                }
            });
        }

        // Helper function untuk mendapatkan nomor minggu
        function getWeekNumber(date) {
            const firstDayOfYear = new Date(date.getFullYear(), 0, 1);
            const pastDaysOfYear = (date - firstDayOfYear) / 86400000;
            return Math.ceil((pastDaysOfYear + firstDayOfYear.getDay() + 1) / 7);
        }

        // Helper function untuk mendapatkan hari pertama minggu
        function firstDayOfWeek(date) {
            const day = date.getDay();
            const diff = date.getDate() - day + (day === 0 ? -6 : 1);
            return new Date(date.setDate(diff));
        }

        // Fungsi untuk filter dropdown
        function toggleFilterDropdown(e) {
            e.stopPropagation();
            filterDropdown.classList.toggle('hidden');
        }

        function closeFilterDropdown() {
            filterDropdown.classList.add('hidden');
        }

        function filterTabungan(e) {
            renderTabungan(e.target.value);
            closeFilterDropdown();
        }
    </script>
</body>
</html>

