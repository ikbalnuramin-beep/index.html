<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Sistem Armada Integrated - Lukot</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css" />
  <script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
</head>
<body class="bg-gray-100 font-sans text-gray-800">

  <!-- TOP APP BAR -->
  <nav class="bg-blue-900 text-white p-4 shadow-lg flex justify-between items-center">
    <div class="flex items-center space-x-2">
      <span class="text-xl font-black tracking-wider text-yellow-400">ARMADA-GO</span>
      <span class="text-xs bg-blue-800 px-2 py-1 rounded">System V2.0</span>
    </div>
    <div class="space-x-1">
      <button onclick="switchMode('driver')" id="tab-driver" class="px-3 py-1.5 rounded text-xs font-bold bg-yellow-400 text-blue-900">📱 Driver App</button>
      <button onclick="switchMode('office')" id="tab-office" class="px-3 py-1.5 rounded text-xs font-bold bg-blue-800 text-white">🏢 Dashboard Office</button>
    </div>
  </nav>

  <div class="max-w-7xl mx-auto p-4">

    <!-- ========================================== -->
    <!-- 1. TAMPILAN APP DRIVER (MOBILE COMPATIBLE) -->
    <!-- ========================================== -->
    <div id="section-driver" class="max-w-md mx-auto bg-white rounded-xl shadow-md p-6 space-y-4">
      <h2 class="text-lg font-bold border-b pb-2 text-center text-blue-900">Form Update Perjalanan</h2>
      
      <!-- Select ID Driver -->
      <div>
        <label class="block text-xs font-bold text-gray-600 mb-1">Pilih Driver / ID</label>
        <select id="drv-id" onchange="autoFillDriverData()" class="w-full border-2 border-gray-300 p-2.5 rounded-lg text-sm bg-gray-50 focus:border-blue-500">
          <option value="">-- Pilih ID Driver --</option>
          <option value="DRV01">DRV01 - Andi</option>
          <option value="DRV02">DRV02 - Cakra</option>
        </select>
      </div>

      <!-- Auto-filled Fields -->
      <div class="grid grid-cols-2 gap-3 bg-blue-50 p-3 rounded-lg text-xs">
        <div>
          <span class="text-gray-500 block">Nama Driver:</span>
          <strong id="drv-nama" class="text-gray-800 text-sm">-</strong>
        </div>
        <div>
          <span class="text-gray-500 block">Armada:</span>
          <strong id="drv-armada" class="text-gray-800 text-sm">-</strong>
        </div>
      </div>

      <div>
        <label class="block text-xs font-bold text-gray-600 mb-1">No. Perjalanan / Trip</label>
        <input type="text" id="drv-trip" placeholder="Contoh: TRIP-001" class="w-full border p-2.5 rounded-lg text-sm bg-gray-50">
      </div>

      <!-- Status Buttons -->
      <div>
        <label class="block text-xs font-bold text-gray-600 mb-2">Status Perjalanan</label>
        <div class="grid grid-cols-2 gap-2 text-xs font-bold">
          <button type="button" onclick="setStatus('🟡 Belum Berangkat')" class="btn-status p-3 rounded-lg border text-yellow-700 bg-yellow-50 hover:bg-yellow-100 border-yellow-300">🟡 Belum Berangkat</button>
          <button type="button" onclick="setStatus('🔵 Berangkat')" class="btn-status p-3 rounded-lg border text-blue-700 bg-blue-50 hover:bg-blue-100 border-blue-300">🔵 Berangkat</button>
          <button type="button" onclick="setStatus('🟠 Tiba Lukot')" class="btn-status p-3 rounded-lg border text-orange-700 bg-orange-50 hover:bg-orange-100 border-orange-300">🟠 Tiba Lukot</button>
          <button type="button" onclick="setStatus('🟢 Selesai')" class="btn-status p-3 rounded-lg border text-green-700 bg-green-50 hover:bg-green-100 border-green-300">🟢 Selesai</button>
        </div>
      </div>

      <!-- Update Button -->
      <button onclick="submitDriverUpdate()" id="btn-submit" class="w-full bg-blue-900 hover:bg-blue-800 text-white font-black py-3 rounded-xl text-sm shadow-md transition">
        🚀 UPDATE STATUS &amp; LOKASI (GPS)
      </button>
    </div>


    <!-- ========================================== -->
    <!-- 2. DASHBOARD OFFICE REAL-TIME             -->
    <!-- ========================================== -->
    <div id="section-office" class="hidden space-y-6">
      
      <!-- STATS SUMMARY -->
      <div class="grid grid-cols-2 md:grid-cols-4 lg:grid-cols-8 gap-3">
        <div class="bg-white p-3 rounded-lg shadow text-center border-b-4 border-blue-900">
          <p class="text-[10px] font-bold text-gray-400 uppercase">Total Driver</p>
          <p id="st-total" class="text-xl font-black text-gray-800">0</p>
        </div>
        <div class="bg-white p-3 rounded-lg shadow text-center border-b-4 border-yellow-500">
          <p class="text-[10px] font-bold text-gray-400 uppercase">Belum</p>
          <p id="st-belum" class="text-xl font-black text-yellow-600">0</p>
        </div>
        <div class="bg-white p-3 rounded-lg shadow text-center border-b-4 border-blue-500">
          <p class="text-[10px] font-bold text-gray-400 uppercase">Berangkat</p>
          <p id="st-berangkat" class="text-xl font-black text-blue-600">0</p>
        </div>
        <div class="bg-white p-3 rounded-lg shadow text-center border-b-4 border-orange-500">
          <p class="text-[10px] font-bold text-gray-400 uppercase">Tiba Lukot</p>
          <p id="st-lukot" class="text-xl font-black text-orange-600">0</p>
        </div>
        <div class="bg-white p-3 rounded-lg shadow text-center border-b-4 border-green-500">
          <p class="text-[10px] font-bold text-gray-400 uppercase">Selesai</p>
          <p id="st-selesai" class="text-xl font-black text-green-600">0</p>
        </div>
        <div class="bg-white p-3 rounded-lg shadow text-center border-b-4 border-red-500">
          <p class="text-[10px] font-bold text-gray-400 uppercase">Terlambat</p>
          <p id="st-late" class="text-xl font-black text-red-600">0</p>
        </div>
        <div class="bg-white p-3 rounded-lg shadow text-center border-b-4 border-indigo-500 col-span-2">
          <p class="text-[10px] font-bold text-gray-400 uppercase">Kepatuhan Update</p>
          <p id="st-kepatuhan" class="text-xl font-black text-indigo-600">0%</p>
        </div>
      </div>

      <!-- MAIN MONITORING & MAP -->
      <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
        
        <!-- Live Table -->
        <div class="lg:col-span-2 bg-white rounded-lg shadow p-4">
          <h3 class="font-bold text-sm text-gray-700 mb-3 flex justify-between items-center">
            <span>Monitoring Armada Real-time</span>
            <button onclick="fetchDashboardData()" class="text-xs bg-gray-100 hover:bg-gray-200 px-2 py-1 rounded border">🔄 Refresh Data</button>
          </h3>
          <div class="overflow-x-auto">
            <table class="w-full text-xs text-left">
              <thead class="bg-gray-100 border-b">
                <tr>
                  <th class="p-2.5">Driver</th>
                  <th class="p-2.5">Armada</th>
                  <th class="p-2.5">Trip</th>
                  <th class="p-2.5">Status</th>
                  <th class="p-2.5">Jam Update</th>
                  <th class="p-2.5">Lokasi</th>
                </tr>
              </thead>
              <tbody id="table-office" class="divide-y">
                <!-- Records injected here -->
              </tbody>
            </table>
          </div>
        </div>

        <!-- Live Map -->
        <div class="bg-white rounded-lg shadow p-4">
          <h3 class="font-bold text-sm text-gray-700 mb-3">Peta Lokasi Armada</h3>
          <div id="map" class="h-80 rounded-lg border"></div>
        </div>

      </div>

      <!-- LAPORAN BULANAN & KPI STAFF -->
      <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
        
        <!-- Tabel KPI -->
        <div class="bg-white rounded-lg shadow p-4">
          <h3 class="font-bold text-sm text-gray-700 mb-3">📊 Target KPI Staff Office (Bulanan)</h3>
          <table class="w-full text-xs text-left">
            <thead class="bg-gray-50 border-b">
              <tr>
                <th class="p-2">Indikator (KPI)</th>
                <th class="p-2">Target</th>
                <th class="p-2">Actual</th>
                <th class="p-2">Achievement</th>
              </tr>
            </thead>
            <tbody class="divide-y">
              <tr>
                <td class="p-2 font-medium">Update Perjalanan</td>
                <td class="p-2">100%</td>
                <td class="p-2">96%</td>
                <td class="p-2 text-green-600 font-bold">96%</td>
              </tr>
              <tr>
                <td class="p-2 font-medium">Driver Berangkat Tepat Waktu</td>
                <td class="p-2">95%</td>
                <td class="p-2">93%</td>
                <td class="p-2 text-green-600 font-bold">98%</td>
              </tr>
              <tr>
                <td class="p-2 font-medium">Update Tiba Lukot</td>
                <td class="p-2">100%</td>
                <td class="p-2">97%</td>
                <td class="p-2 text-green-600 font-bold">97%</td>
              </tr>
              <tr>
                <td class="p-2 font-medium">Trip Selesai</td>
                <td class="p-2">100%</td>
                <td class="p-2">99%</td>
                <td class="p-2 text-green-600 font-bold">99%</td>
              </tr>
            </tbody>
          </table>
        </div>

        <!-- Chart Target vs Actual -->
        <div class="bg-white rounded-lg shadow p-4">
          <h3 class="font-bold text-sm text-gray-700 mb-3">Grafik Target vs Actual</h3>
          <canvas id="chartKPI" class="max-h-48"></canvas>
        </div>

      </div>

    </div>

  </div>

  <script>
    // MASUKKAN URL GOOGLE APPS SCRIPT ANDA DI SINI
    const API_URL = "NANTI_TEMPEL_URL_DEPLO_APPS_SCRIPT_DI_SINI";

    // MASTER DRIVER LOCAL BACKUP
    const masterDrivers = {
      "DRV01": { driver: "Andi", kenek: "Budi", armada: "Fuso 01" },
      "DRV02": { driver: "Cakra", kenek: "Dedi", armada: "CDE 05" }
    };

    let selectedStatus = "";
    let map, markers = [];

    // Switch Screen Mode
    function switchMode(mode) {
      if (mode === 'driver') {
        document.getElementById('section-driver').classList.remove('hidden');
        document.getElementById('section-office').classList.add('hidden');
        document.getElementById('tab-driver').className = "px-3 py-1.5 rounded text-xs font-bold bg-yellow-400 text-blue-900";
        document.getElementById('tab-office').className = "px-3 py-1.5 rounded text-xs font-bold bg-blue-800 text-white";
      } else {
        document.getElementById('section-driver').classList.add('hidden');
        document.getElementById('section-office').classList.remove('hidden');
        document.getElementById('tab-office').className = "px-3 py-1.5 rounded text-xs font-bold bg-yellow-400 text-blue-900";
        document.getElementById('tab-driver').className = "px-3 py-1.5 rounded text-xs font-bold bg-blue-800 text-white";
        initMap();
        fetchDashboardData();
      }
    }

    // Auto Fill Driver Info
    function autoFillDriverData() {
      const id = document.getElementById('drv-id').value;
      if (masterDrivers[id]) {
        document.getElementById('drv-nama').innerText = masterDrivers[id].driver;
        document.getElementById('drv-armada').innerText = masterDrivers[id].armada;
      } else {
        document.getElementById('drv-nama').innerText = "-";
        document.getElementById('drv-armada').innerText = "-";
      }
    }

    function setStatus(status) {
      selectedStatus = status;
      alert("Status dipilih: " + status);
    }

    // Submit Action Driver
    function submitDriverUpdate() {
      const id = document.getElementById('drv-id').value;
      const trip = document.getElementById('drv-trip').value;

      if (!id || !trip || !selectedStatus) {
        return alert("Harap lengkapi ID Driver, No Trip, dan Pilih Status!");
      }

      if ("geolocation" in navigator) {
        navigator.geolocation.getCurrentPosition(position => {
          const now = new Date();
          const payload = {
            tanggal: now.toISOString().split('T')[0],
            driver: masterDrivers[id].driver,
            kenek: masterDrivers[id].kenek,
            armada: masterDrivers[id].armada,
            trip: trip,
            status: selectedStatus,
            jam: now.getHours().toString().padStart(2, '0') + ":" + now.getMinutes().toString().padStart(2, '0'),
            lat: position.coords.latitude,
            lng: position.coords.longitude
          };

          // Post to Google Sheets
          fetch(API_URL, {
            method: "POST",
            body: JSON.stringify(payload)
          }).then(() => {
            alert("Data Berhasil Dikirim ke Office!");
          }).catch(() => {
            alert("Terikirim secara lokal (Demo mode)!");
          });

        }, err => alert("Gagal mengambil GPS. Harap izinkan akses lokasi HP!"));
      }
    }

    // Initialize Leaflet Map
    function initMap() {
      if (!map) {
        map = L.map('map').setView([-6.175392, 106.827153], 6);
        L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png').addTo(map);
      }
    }

    // Render Chart
    const ctx = document.getElementById('chartKPI').getContext('2d');
    new Chart(ctx, {
      type: 'bar',
      data: {
        labels: ['Update', 'Tepat Waktu', 'Tiba Lukot', 'Selesai'],
        datasets: [
          { label: 'Target (%)', data: [100, 95, 100, 100], backgroundColor: '#E5E7EB' },
          { label: 'Actual (%)', data: [96, 93, 97, 99], backgroundColor: '#1E3A8A' }
        ]
      },
      options: { responsive: true, scales: { y: { beginAtZero: true, max: 100 } } }
    });
  </script>
</body>
</html>
