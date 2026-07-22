
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Keluarga Besar Wheel</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif; }
        .active-tab { color: #2563eb; font-weight: bold; }
        
        /* Freeze column untuk tabel iuran */
        .sticky-col {
            position: sticky;
            left: 0;
            background-color: white;
            z-index: 10;
        }
        th.sticky-col {
            background-color: #2563eb;
            color: white;
            z-index: 20;
        }
    </style>
</head>
<body class="bg-gray-100 min-h-screen pb-20">

    <!-- LOGIN SCREEN -->
    <div id="login-screen" class="fixed inset-0 bg-gradient-to-b from-blue-600 to-blue-800 z-50 flex flex-col items-center justify-center p-4">
        <div class="text-center text-white mb-6">
            <div class="w-20 h-20 bg-white/20 rounded-2xl flex items-center justify-center mx-auto mb-3 backdrop-blur-md">
                <i class="fas fa-users text-4xl text-white"></i>
            </div>
            <h1 class="text-2xl font-bold">Keluarga Besar Wheel</h1>
            <p class="text-sm opacity-80">Sistem Laporan Keuangan Keluarga</p>
        </div>

        <div class="bg-white w-full max-w-sm rounded-2xl p-6 shadow-xl text-center">
            <h2 class="text-lg font-bold text-gray-800 mb-4">Masuk ke Aplikasi</h2>
            
            <div class="mb-3 text-left">
                <label class="block text-xs font-semibold text-gray-600 mb-1">Masuk Sebagai</label>
                <select id="role-select" class="w-full px-4 py-2 border rounded-xl focus:ring-2 focus:ring-blue-500 text-sm">
                    <option value="anggota">Anggota</option>
                    <option value="admin">Admin</option>
                </select>
            </div>

            <div class="mb-4 text-left">
                <label class="block text-xs font-semibold text-gray-600 mb-1">Password</label>
                <input type="password" id="password-input" placeholder="••••••••" class="w-full px-4 py-2.5 border rounded-xl focus:ring-2 focus:ring-blue-500 text-sm">
            </div>

            <button onclick="login()" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-semibold py-3 rounded-xl transition text-sm">Masuk</button>
            <p class="text-xs text-gray-400 mt-4">Keluarga Besar Wheel © <span class="current-year-txt">2026</span></p>
        </div>
    </div>

    <!-- MODAL BUKTI TRANSFER -->
    <div id="modal-bukti" class="fixed inset-0 bg-black/70 z-50 flex items-center justify-center p-4 hidden">
        <div class="bg-white w-full max-w-sm rounded-2xl p-4 shadow-xl relative">
            <button onclick="closeModalBukti()" class="absolute top-3 right-3 text-gray-500 hover:text-gray-800 font-bold text-lg">&times;</button>
            <h3 class="font-bold text-gray-800 text-sm mb-3"><i class="fas fa-receipt mr-1 text-blue-600"></i> Bukti Transfer</h3>
            <div class="p-2 border rounded-xl bg-gray-50 text-center">
                <img id="img-bukti-target" src="" alt="Bukti Transfer" class="max-h-96 mx-auto rounded-lg object-contain">
                <p id="txt-bukti-ket" class="text-xs text-gray-600 mt-2 font-medium"></p>
            </div>
        </div>
    </div>

    <!-- MAIN APP -->
    <div id="app" class="max-w-md mx-auto bg-gray-50 min-h-screen shadow-md relative hidden">
        
        <!-- HEADER -->
        <div class="bg-blue-600 text-white p-4 rounded-b-2xl shadow-lg">
            <div class="flex justify-between items-center mb-2">
                <div class="flex items-center space-x-2">
                    <i class="fas fa-users text-2xl"></i>
                    <h1 class="text-xl font-bold">Keluarga Besar Wheel</h1>
                </div>
                <button onclick="logout()" title="Keluar" class="bg-red-500 p-2 rounded-lg text-xs hover:bg-red-600"><i class="fas fa-sign-out-alt"></i></button>
            </div>
            <div class="flex justify-between items-center text-xs text-blue-100">
                <p>Status: <b id="user-role-label" class="capitalize">Anggota</b></p>
                <div class="flex items-center space-x-1">
                    <span>Tahun:</span>
                    <select id="filter-tahun" onchange="updateAllData()" class="bg-blue-700 text-white text-xs rounded px-1 py-0.5 border border-blue-400">
                        <!-- Auto Dynamic Years -->
                    </select>
                </div>
            </div>
        </div>

        <!-- TAB: BERANDA -->
        <div id="tab-beranda" class="p-4 space-y-4">
            <div class="bg-emerald-600 text-white p-4 rounded-2xl flex justify-between items-center shadow">
                <div>
                    <p class="text-xs opacity-90">Saldo Kas Saat Ini</p>
                    <p class="text-2xl font-bold" id="total-saldo-display">Rp 0</p>
                </div>
                <div class="w-10 h-10 bg-yellow-400 text-yellow-900 rounded-full flex items-center justify-center font-bold text-xl">$</div>
            </div>

            <!-- Panel Konfirmasi Laporan Khusus Admin -->
            <div id="admin-approval-panel" class="bg-amber-50 border border-amber-200 rounded-2xl p-3 hidden">
                <h3 class="text-xs font-bold text-amber-800 mb-2"><i class="fas fa-user-shield mr-1"></i> Panel Admin: Persetujuan Laporan</h3>
                <div id="admin-laporan-list" class="space-y-2">
                    <!-- Dynamic List via JavaScript -->
                </div>
            </div>

            <!-- Grid Summary -->
            <div class="grid grid-cols-2 gap-3">
                <div class="bg-blue-600 text-white p-3 rounded-2xl">
                    <p class="text-xs">Bayar Bulan Ini</p>
                    <p class="text-xl font-bold mt-1" id="stat-bayar-bulan">0 / 0 <span class="text-xs font-normal">kk</span></p>
                </div>
                <div class="bg-purple-600 text-white p-3 rounded-2xl">
                    <p class="text-xs">Iuran Bulan Ini</p>
                    <p class="text-xl font-bold mt-1" id="stat-iuran-bulan">Rp 0</p>
                </div>
                <div class="bg-sky-500 text-white p-3 rounded-2xl">
                    <p class="text-xs">Total Iuran Masuk</p>
                    <p class="text-xl font-bold mt-1" id="stat-total-iuran">Rp 0</p>
                </div>
                <div class="bg-orange-500 text-white p-3 rounded-2xl">
                    <p class="text-xs">Total Pengeluaran</p>
                    <p class="text-xl font-bold mt-1" id="stat-total-pengeluaran">Rp 0</p>
                </div>
            </div>

            <!-- Card Rekening Pembayaran -->
            <div class="bg-slate-800 text-white p-4 rounded-2xl shadow">
                <p class="text-xs text-gray-300 font-semibold mb-1"><i class="fas fa-university mr-1"></i> REKENING PEMBAYARAN</p>
                <p class="text-xl font-mono tracking-wider font-bold">7261894038</p>
                <p class="text-sm text-gray-300">a.n. Suyatmo</p>
                <span class="inline-block bg-teal-600 text-xs px-2 py-0.5 rounded mt-2 font-bold">BANK BSI</span>
            </div>
        </div>

        <!-- TAB: IURAN -->
        <div id="tab-iuran" class="p-4 hidden space-y-4">
            <div class="flex justify-between items-center">
                <h2 class="text-lg font-bold">Laporan Iuran Warga</h2>
                <span class="text-xs bg-blue-100 text-blue-700 px-2 py-1 rounded-full font-bold" id="total-anggota-count">0 Anggota</span>
            </div>

            <!-- Form Kelola Anggota (Khusus Admin) -->
            <div id="admin-tambah-anggota-form" class="bg-white p-3 rounded-xl shadow border hidden space-y-2">
                <p class="text-xs font-bold text-gray-700"><i class="fas fa-user-plus mr-1"></i> Tambah Anggota Baru</p>
                <div class="flex gap-2">
                    <input type="text" id="input-nama-anggota-baru" placeholder="Nama Anggota..." class="w-full p-2 border rounded-lg text-xs">
                    <button onclick="tambahAnggota()" class="bg-blue-600 text-white px-3 py-2 rounded-lg text-xs font-bold">Tambah</button>
                </div>
            </div>

            <!-- Tabel Iuran dengan Freeze Column & Horizontal Scroll -->
            <div class="overflow-x-auto bg-white rounded-xl shadow border">
                <table class="w-full text-left text-xs border-collapse">
                    <thead class="bg-blue-600 text-white">
                        <tr id="tabel-iuran-header">
                            <th class="p-2 border-b">No</th>
                            <th class="p-2 border-b sticky-col">Nama</th>
                            <!-- Bulan Jan - Des akan diproduksi otomatis -->
                        </tr>
                    </thead>
                    <tbody id="tabel-iuran-body" class="divide-y">
                        <!-- Dynamic Content -->
                    </tbody>
                </table>
            </div>
        </div>

        <!-- TAB: LAPOR -->
        <div id="tab-lapor" class="p-4 space-y-4 hidden">
            <h2 class="text-lg font-bold">Lapor Pembayaran Iuran</h2>
            <form onsubmit="kirimLaporan(event)" class="bg-white p-4 rounded-xl shadow space-y-3">
                <div>
                    <label class="text-xs font-semibold text-gray-600">Pilih Nama Anggota</label>
                    <select id="input-nama" required class="w-full p-2 border rounded-lg text-sm"></select>
                </div>
                <div class="grid grid-cols-2 gap-2">
                    <div>
                        <label class="text-xs font-semibold text-gray-600">Nominal (Rp)</label>
                        <input type="number" id="input-nominal" value="50000" required class="w-full p-2 border rounded-lg text-sm">
                    </div>
                    <div>
                        <label class="text-xs font-semibold text-gray-600">Bulan</label>
                        <select id="input-bulan" class="w-full p-2 border rounded-lg text-sm"></select>
                    </div>
                </div>
                <div>
                    <label class="text-xs font-semibold text-gray-600">Upload Bukti Transfer</label>
                    <input type="file" id="input-file-bukti" accept="image/*" required class="w-full text-xs p-2 border rounded-lg">
                </div>
                <button type="submit" class="w-full bg-blue-600 text-white py-2.5 rounded-lg font-bold text-xs uppercase tracking-wide">Kirim Laporan Ke Admin</button>
            </form>
        </div>

        <!-- TAB: KAS MENU (Sub-menu Kas) -->
        <div id="tab-kas" class="p-4 space-y-4 hidden">
            
            <!-- Menu Navigasi Sub Kas -->
            <div class="flex bg-white rounded-xl p-1 shadow border text-xs font-bold">
                <button onclick="switchKasSubTab('dana-sosial')" id="subnav-dana-sosial" class="flex-1 py-2 rounded-lg text-center bg-blue-600 text-white">❤️ Dana Sosial</button>
                <button onclick="switchKasSubTab('pengeluaran')" id="subnav-pengeluaran" class="flex-1 py-2 rounded-lg text-center text-gray-600">💸 Pengeluaran</button>
                <button onclick="switchKasSubTab('laporan-kas')" id="subnav-laporan-kas" class="flex-1 py-2 rounded-lg text-center text-gray-600">📊 Laporan Kas</button>
            </div>

            <!-- SUB TAB 1: DANA SOSIAL -->
            <div id="kas-sub-dana-sosial" class="space-y-3">
                <div class="bg-rose-50 border border-rose-200 p-3 rounded-xl flex justify-between items-center">
                    <div>
                        <p class="text-xs text-rose-700 font-semibold">Total Penggunaan Dana Sosial</p>
                        <p class="text-xl font-bold text-rose-800" id="total-dana-sosial-txt">Rp 0</p>
                    </div>
                    <i class="fas fa-heart text-2xl text-rose-500"></i>
                </div>

                <!-- Input Admin Dana Sosial -->
                <div id="admin-dansos-form" class="bg-white p-3 rounded-xl shadow border hidden space-y-2">
                    <p class="text-xs font-bold text-rose-600"><i class="fas fa-plus-circle mr-1"></i> Input Dana Sosial Baru (Admin)</p>
                    <div class="grid grid-cols-2 gap-2">
                        <select id="input-kategori-dansos" class="p-2 border rounded-lg text-xs">
                            <option value="Takziah">Takziah</option>
                            <option value="Sakit">Sakit</option>
                            <option value="Bencana">Bencana</option>
                            <option value="Lainnya">Lainnya</option>
                        </select>
                        <input type="number" id="input-nominal-dansos" placeholder="Nominal (Rp)..." class="p-2 border rounded-lg text-xs">
                    </div>
                    <input type="text" id="input-ket-dansos" placeholder="Keterangan..." class="w-full p-2 border rounded-lg text-xs">
                    <button onclick="tambahDanaSosial()" class="w-full bg-rose-600 text-white py-1.5 rounded-lg text-xs font-bold">Simpan Dana Sosial</button>
                </div>

                <div class="bg-white rounded-xl shadow border overflow-hidden">
                    <table class="w-full text-left text-xs">
                        <thead class="bg-rose-500 text-white">
                            <tr>
                                <th class="p-2">Tgl</th>
                                <th class="p-2">Kategori</th>
                                <th class="p-2">Keterangan</th>
                                <th class="p-2 text-right">Jumlah</th>
                            </tr>
                        </thead>
                        <tbody id="list-dana-sosial-body" class="divide-y"></tbody>
                    </table>
                </div>
            </div>

            <!-- SUB TAB 2: PENGELUARAN -->
            <div id="kas-sub-pengeluaran" class="space-y-3 hidden">
                <div class="bg-orange-50 border border-orange-200 p-3 rounded-xl flex justify-between items-center">
                    <div>
                        <p class="text-xs text-orange-700 font-semibold">Total Pengeluaran Operasional</p>
                        <p class="text-xl font-bold text-orange-800" id="total-pengeluaran-txt">Rp 0</p>
                    </div>
                    <i class="fas fa-wallet text-2xl text-orange-500"></i>
                </div>

                <!-- Input Admin Pengeluaran -->
                <div id="admin-pengeluaran-form" class="bg-white p-3 rounded-xl shadow border hidden space-y-2">
                    <p class="text-xs font-bold text-orange-600"><i class="fas fa-minus-circle mr-1"></i> Input Pengeluaran Baru (Admin)</p>
                    <input type="text" id="input-ket-pengeluaran" placeholder="Keterangan Pengeluaran..." class="w-full p-2 border rounded-lg text-xs">
                    <div class="grid grid-cols-2 gap-2">
                        <input type="number" id="input-nominal-pengeluaran" placeholder="Nominal (Rp)..." class="p-2 border rounded-lg text-xs">
                        <input type="file" id="input-bukti-pengeluaran" accept="image/*" class="p-1 border rounded-lg text-xs">
                    </div>
                    <button onclick="tambahPengeluaran()" class="w-full bg-orange-600 text-white py-1.5 rounded-lg text-xs font-bold">Simpan Pengeluaran</button>
                </div>

                <div class="bg-white rounded-xl shadow border overflow-hidden">
                    <table class="w-full text-left text-xs">
                        <thead class="bg-orange-500 text-white">
                            <tr>
                                <th class="p-2">Tgl</th>
                                <th class="p-2">Keterangan</th>
                                <th class="p-2 text-right">Jumlah</th>
                                <th class="p-2 text-center">Bukti</th>
                            </tr>
                        </thead>
                        <tbody id="list-pengeluaran-body" class="divide-y"></tbody>
                    </table>
                </div>
            </div>

            <!-- SUB TAB 3: LAPORAN KAS -->
            <div id="kas-sub-laporan-kas" class="space-y-3 hidden">
                <div class="bg-white p-3 rounded-xl shadow border">
                    <h3 class="font-bold text-sm text-gray-800 mb-2"><i class="fas fa-chart-line text-blue-600 mr-1"></i> Ringkasan Arus Kas Bulanan</h3>
                    <div class="overflow-x-auto">
                        <table class="w-full text-left text-[11px] border-collapse">
                            <thead class="bg-blue-600 text-white">
                                <tr>
                                    <th class="p-1.5 border">Bulan</th>
                                    <th class="p-1.5 border text-right">Iuran Masuk</th>
                                    <th class="p-1.5 border text-right">Pengeluaran</th>
                                    <th class="p-1.5 border text-right">Dana Sosial</th>
                                    <th class="p-1.5 border text-right">Saldo</th>
                                </tr>
                            </thead>
                            <tbody id="tabel-laporan-kas-body" class="divide-y"></tbody>
                        </table>
                    </div>
                </div>
            </div>

        </div>

        <!-- TAB: KEGIATAN -->
        <div id="tab-kegiatan" class="p-4 hidden space-y-3">
            <h2 class="text-lg font-bold">Kegiatan Warga</h2>
            <div class="bg-white p-3 rounded-xl shadow border text-xs">
                <p class="font-bold text-blue-600">Silaturahmi & Arisan Rutin</p>
                <p class="text-gray-400 text-[10px] mt-0.5">Setiap Awal Bulan</p>
                <p class="mt-2 text-gray-700">Dokumentasi kegiatan dan notulensi rapat warga akan diperbarui di sini secara berkala.</p>
            </div>
        </div>

        <!-- BOTTOM NAVIGATION -->
        <div class="fixed bottom-0 left-1/2 -translate-x-1/2 w-full max-w-md bg-white border-t flex justify-around items-center py-2 text-xs text-gray-500 z-40">
            <button onclick="switchTab('beranda')" id="btn-beranda" class="flex flex-col items-center active-tab">
                <i class="fas fa-home text-lg"></i>
                <span>Beranda</span>
            </button>
            <button onclick="switchTab('iuran')" id="btn-iuran" class="flex flex-col items-center">
                <i class="fas fa-th-large text-lg"></i>
                <span>Iuran</span>
            </button>
            <button onclick="switchTab('lapor')" id="btn-lapor" class="flex flex-col items-center -mt-5">
                <div class="bg-blue-600 text-white p-3 rounded-full shadow-lg">
                    <i class="fas fa-file-medical text-xl"></i>
                </div>
                <span class="mt-1 font-bold text-blue-600">Lapor</span>
            </button>
            <button onclick="switchTab('kas')" id="btn-kas" class="flex flex-col items-center">
                <i class="fas fa-chart-bar text-lg"></i>
                <span>Kas</span>
            </button>
            <button onclick="switchTab('kegiatan')" id="btn-kegiatan" class="flex flex-col items-center">
                <i class="fas fa-book text-lg"></i>
                <span>Kegiatan</span>
            </button>
        </div>

    </div>

    <script>
        // Data Awal 62 Anggota
        const defaultAnggota = [
            "Abdillah Zaqi", "Ade S", "Agus Winarno", "Akhmad Nurrokhim", "Alfa", "Amin S", "Ananta", "Anjasmara", 
            "Apriyanto", "Arief W", "Asep S", "Bayu E.", "Beni", "Carmuto", "Diki R", "Dodi Eka S", "Erik", "Erwin", 
            "Fajar A. M", "Falah", "Feri I", "Feri sn", "Gagas", "Ghofur", "Hadriansyahh", "Habil", "Hendra K", 
            "Heri Kurniawan", "Indra Kamaju", "Indra S", "Juan", "Kemas", "Khamdani", "Luqmanul hakim", "Mirza", 
            "M. Reynaldi", "Nanang Kurniawan", "Nicko apriyansyah", "Nurtri", "Okta Kamaju", "Rama", "Ragil", 
            "Rahmat febrianto", "Redy Aprianto", "Rezon", "Rihdo", "Rismanto", "Rizqi Fajar", "Slamet J", "Srimarbowo", 
            "Suherdyanto", "Suyatmo", "Titus", "Tur P.", "Wafiq", "Wahyu Luthfi", "Yogi I", "Yudiansyah", "Yudi H", 
            "Yudi A", "Yusa"
        ];

        const bulanList = ["Jan", "Feb", "Mar", "Apr", "Mei", "Jun", "Jul", "Agu", "Sep", "Okt", "Nov", "Des"];

        // State & LocalStorage Management
        let anggotaList = JSON.parse(localStorage.getItem('anggotaList')) || defaultAnggota;
        let passAnggota = localStorage.getItem('passAnggota') || '12345';
        let passAdmin = localStorage.getItem('passAdmin') || '12345';
        let currentRole = '';

        let iuranData = JSON.parse(localStorage.getItem('iuranData')) || {}; 
        let laporanPending = JSON.parse(localStorage.getItem('laporanPending')) || [];
        let pengeluaranList = JSON.parse(localStorage.getItem('pengeluaranList')) || [];
        let danaSosialList = JSON.parse(localStorage.getItem('danaSosialList')) || [];

        // Auto Year Initialization
        const currentYear = new Date().getFullYear();
        let selectedYear = currentYear;

        function initYears() {
            const selectFilter = document.getElementById('filter-tahun');
            selectFilter.innerHTML = '';
            for(let y = currentYear - 2; y <= currentYear + 2; y++) {
                selectFilter.innerHTML += `<option value="${y}" ${y === currentYear ? 'selected' : ''}>${y}</option>`;
            }
            document.querySelectorAll('.current-year-txt').forEach(el => el.innerText = currentYear);

            const selectBulan = document.getElementById('input-bulan');
            selectBulan.innerHTML = '';
            bulanList.forEach(b => {
                selectBulan.innerHTML += `<option value="${b}">${b}</option>`;
            });
        }

        function login() {
            const role = document.getElementById('role-select').value;
            const inputPw = document.getElementById('password-input').value;

            if (role === 'anggota' && inputPw === passAnggota) {
                currentRole = 'anggota';
            } else if (role === 'admin' && inputPw === passAdmin) {
                currentRole = 'admin';
            } else {
                alert('Password salah! (Default: 12345)');
                return;
            }

            document.getElementById('login-screen').classList.add('hidden');
            document.getElementById('app').classList.remove('hidden');
            document.getElementById('user-role-label').innerText = currentRole;
            
            initYears();
            updateAllData();
        }

        function logout() {
            document.getElementById('password-input').value = '';
            document.getElementById('login-screen').classList.remove('hidden');
            document.getElementById('app').classList.add('hidden');
        }

        function tambahAnggota() {
            const namaInput = document.getElementById('input-nama-anggota-baru');
            const nama = namaInput.value.trim();
            if(!nama) return alert('Masukkan nama!');

            if(anggotaList.includes(nama)) return alert('Nama sudah ada!');

            anggotaList.push(nama);
            localStorage.setItem('anggotaList', JSON.stringify(anggotaList));
            namaInput.value = '';
            updateAllData();
            alert('Anggota berhasil ditambahkan!');
        }

        function kirimLaporan(e) {
            e.preventDefault();
            const nama = document.getElementById('input-nama').value;
            const nominal = parseInt(document.getElementById('input-nominal').value);
            const bulan = document.getElementById('input-bulan').value;
            const year = document.getElementById('filter-tahun').value;
            const fileInput = document.getElementById('input-file-bukti');

            if(fileInput.files && fileInput.files[0]) {
                const reader = new FileReader();
                reader.onload = function(e) {
                    const buktiBase64 = e.target.result;
                    const laporanBaru = { id: Date.now(), nama, nominal, bulan, tahun: year, bukti: buktiBase64 };
                    laporanPending.push(laporanBaru);
                    localStorage.setItem('laporanPending', JSON.stringify(laporanPending));
                    
                    alert('Laporan berhasil dikirim ke Admin!');
                    fileInput.value = '';
                    switchTab('beranda');
                    updateAllData();
                };
                reader.readAsDataURL(fileInput.files[0]);
            }
        }

        function setujuiLaporan(id) {
            const lap = laporanPending.find(item => item.id === id);
            if(lap) {
                if(!iuranData[lap.tahun]) iuranData[lap.tahun] = {};
                if(!iuranData[lap.tahun][lap.nama]) iuranData[lap.tahun][lap.nama] = {};
                
                iuranData[lap.tahun][lap.nama][lap.bulan] = {
                    nominal: lap.nominal,
                    bukti: lap.bukti
                };

                localStorage.setItem('iuranData', JSON.stringify(iuranData));

                laporanPending = laporanPending.filter(item => item.id !== id);
                localStorage.setItem('laporanPending', JSON.stringify(laporanPending));
                
                alert('Laporan disetujui!');
                updateAllData();
            }
        }

        function hapusLaporan(id) {
            if (confirm('Yakin hapus laporan ini?')) {
                laporanPending = laporanPending.filter(item => item.id !== id);
                localStorage.setItem('laporanPending', JSON.stringify(laporanPending));
                updateAllData();
            }
        }

        function tambahDanaSosial() {
            const kat = document.getElementById('input-kategori-dansos').value;
            const nom = parseInt(document.getElementById('input-nominal-dansos').value);
            const ket = document.getElementById('input-ket-dansos').value.trim();

            if(!nom || !ket) return alert('Isi nominal dan keterangan!');

            danaSosialList.push({ id: Date.now(), kat, nom, ket, tgl: new Date().toLocaleDateString('id-ID') });
            localStorage.setItem('danaSosialList', JSON.stringify(danaSosialList));

            document.getElementById('input-nominal-dansos').value = '';
            document.getElementById('input-ket-dansos').value = '';
            updateAllData();
            alert('Dana Sosial berhasil dicatat!');
        }

        function tambahPengeluaran() {
            const ket = document.getElementById('input-ket-pengeluaran').value.trim();
            const nom = parseInt(document.getElementById('input-nominal-pengeluaran').value);
            const fileInput = document.getElementById('input-bukti-pengeluaran');

            if(!ket || !nom) return alert('Isi keterangan dan nominal pengeluaran!');

            const processSave = (buktiUrl = '') => {
                pengeluaranList.push({ id: Date.now(), ket, nom, bukti: buktiUrl, tgl: new Date().toLocaleDateString('id-ID') });
                localStorage.setItem('pengeluaranList', JSON.stringify(pengeluaranList));

                document.getElementById('input-ket-pengeluaran').value = '';
                document.getElementById('input-nominal-pengeluaran').value = '';
                fileInput.value = '';
                updateAllData();
                alert('Pengeluaran berhasil dicatat!');
            };

            if(fileInput.files && fileInput.files[0]) {
                const reader = new FileReader();
                reader.onload = (e) => processSave(e.target.result);
                reader.readAsDataURL(fileInput.files[0]);
            } else {
                processSave('');
            }
        }

        function showBuktiModal(imgSrc, ketStr) {
            document.getElementById('img-bukti-target').src = imgSrc;
            document.getElementById('txt-bukti-ket').innerText = ketStr;
            document.getElementById('modal-bukti').classList.remove('hidden');
        }

        function closeModalBukti() {
            document.getElementById('modal-bukti').classList.add('hidden');
        }

        function updateAllData() {
            selectedYear = document.getElementById('filter-tahun').value || currentYear;
            const yearData = iuranData[selectedYear] || {};

            // Render Table Header (Jan-Des)
            const headerTr = document.getElementById('tabel-iuran-header');
            headerTr.innerHTML = `<th class="p-2 border-b">No</th><th class="p-2 border-b sticky-col">Nama</th>`;
            bulanList.forEach(b => {
                headerTr.innerHTML += `<th class="p-2 border-b text-center min-w-[65px]">${b}</th>`;
            });

            // Calculate Totals
            let totalIuranMasuk = 0;
            let bayarBulanIniCount = 0;
            let totalIuranBulanIni = 0;
            const currentMonthIdx = new Date().getMonth();
            const currentMonthName = bulanList[currentMonthIdx];

            // Render Tabel Iuran Body
            const tbody = document.getElementById('tabel-iuran-body');
            tbody.innerHTML = '';

            anggotaList.forEach((nama, idx) => {
                const userYearData = yearData[nama] || {};
                let rowHtml = `<tr><td class="p-2 text-gray-500">${idx+1}</td><td class="p-2 font-semibold text-gray-800 sticky-col">${nama}</td>`;

                bulanList.forEach(b => {
                    const pay = userYearData[b];
                    if(pay && pay.nominal > 0) {
                        totalIuranMasuk += pay.nominal;
                        if(b === currentMonthName) {
                            bayarBulanIniCount++;
                            totalIuranBulanIni += pay.nominal;
                        }
                        rowHtml += `
                            <td class="p-2 text-center">
                                <div class="text-green-600 font-bold">✓</div>
                                ${pay.bukti ? `<button onclick="showBuktiModal('${pay.bukti}', '${nama} - ${b}')" class="text-[9px] bg-blue-100 text-blue-700 px-1 rounded hover:bg-blue-200">Bukti</button>` : ''}
                            </td>
                        `;
                    } else {
                        rowHtml += `<td class="p-2 text-center text-gray-300">—</td>`;
                    }
                });

                rowHtml += `</tr>`;
                tbody.innerHTML += rowHtml;
            });

            // Calculate Totals Pengeluaran & Dansos
            const totalDansos = danaSosialList.reduce((sum, item) => sum + item.nom, 0);
            const totalPengeluaran = pengeluaranList.reduce((sum, item) => sum + item.nom, 0);
            const saldoAkhir = totalIuranMasuk - (totalPengeluaran + totalDansos);

            // Update Dashboards
            document.getElementById('total-saldo-display').innerText = `Rp ${saldoAkhir.toLocaleString('id-ID')}`;
            document.getElementById('stat-bayar-bulan').innerHTML = `${bayarBulanIniCount} / ${anggotaList.length} <span class="text-xs font-normal">kk</span>`;
            document.getElementById('stat-iuran-bulan').innerText = `Rp ${totalIuranBulanIni.toLocaleString('id-ID')}`;
            document.getElementById('stat-total-iuran').innerText = `Rp ${totalIuranMasuk.toLocaleString('id-ID')}`;
            document.getElementById('stat-total-pengeluaran').innerText = `Rp ${(totalPengeluaran + totalDansos).toLocaleString('id-ID')}`;

            document.getElementById('total-dana-sosial-txt').innerText = `Rp ${totalDansos.toLocaleString('id-ID')}`;
            document.getElementById('total-pengeluaran-txt').innerText = `Rp ${totalPengeluaran.toLocaleString('id-ID')}`;

            // Dropdown Lapor
            const selectNama = document.getElementById('input-nama');
            selectNama.innerHTML = '';
            anggotaList.sort().forEach(n => selectNama.innerHTML += `<option value="${n}">${n}</option>`);
            document.getElementById('total-anggota-count').innerText = `${anggotaList.length} Anggota`;

            // Admin Controls
            if(currentRole === 'admin') {
                document.getElementById('admin-approval-panel').classList.remove('hidden');
                document.getElementById('admin-tambah-anggota-form').classList.remove('hidden');
                document.getElementById('admin-dansos-form').classList.remove('hidden');
                document.getElementById('admin-pengeluaran-form').classList.remove('hidden');

                // Approval List
                const container = document.getElementById('admin-laporan-list');
                container.innerHTML = '';
                if (laporanPending.length === 0) {
                    container.innerHTML = '<p class="text-xs text-gray-500">Tidak ada laporan yang perlu disetujui.</p>';
                } else {
                    laporanPending.forEach(item => {
                        container.innerHTML += `
                            <div class="bg-white p-2.5 rounded-lg border flex justify-between items-center text-xs shadow-sm">
                                <div>
                                    <p class="font-bold text-gray-800">${item.nama}</p>
                                    <p class="text-gray-500">${item.bulan} ${item.tahun} - Rp ${item.nominal.toLocaleString('id-ID')}</p>
                                    ${item.bukti ? `<button onclick="showBuktiModal('${item.bukti}', '${item.nama}')" class="text-[10px] text-blue-600 underline">Lihat Bukti</button>` : ''}
                                </div>
                                <div class="flex space-x-1">
                                    <button onclick="setujuiLaporan(${item.id})" class="bg-green-600 text-white px-2 py-1 rounded font-bold"><i class="fas fa-check"></i></button>
                                    <button onclick="hapusLaporan(${item.id})" class="bg-red-500 text-white px-2 py-1 rounded"><i class="fas fa-trash"></i></button>
                                </div>
                            </div>
                        `;
                    });
                }
            } else {
                document.getElementById('admin-approval-panel').classList.add('hidden');
                document.getElementById('admin-tambah-anggota-form').classList.add('hidden');
                document.getElementById('admin-dansos-form').classList.add('hidden');
                document.getElementById('admin-pengeluaran-form').classList.add('hidden');
            }

            // Render Sub Tab Content
            renderDanaSosialBody();
            renderPengeluaranBody();
            renderLaporanKasBody();
        }

        function renderDanaSosialBody() {
            const body = document.getElementById('list-dana-sosial-body');
            body.innerHTML = '';
            if(danaSosialList.length === 0) {
                body.innerHTML = `<tr><td colspan="4" class="p-3 text-center text-gray-400">Belum ada penggunaan dana sosial</td></tr>`;
            } else {
                danaSosialList.slice().reverse().forEach(d => {
                    body.innerHTML += `
                        <tr>
                            <td class="p-2 text-gray-500">${d.tgl}</td>
                            <td class="p-2 font-bold text-rose-600">${d.kat}</td>
                            <td class="p-2">${d.ket}</td>
                            <td class="p-2 text-right font-bold text-rose-700">Rp ${d.nom.toLocaleString('id-ID')}</td>
                        </tr>
                    `;
                });
            }
        }

        function renderPengeluaranBody() {
            const body = document.getElementById('list-pengeluaran-body');
            body.innerHTML = '';
            if(pengeluaranList.length === 0) {
                body.innerHTML = `<tr><td colspan="4" class="p-3 text-center text-gray-400">Belum ada pengeluaran operasional</td></tr>`;
            } else {
                pengeluaranList.slice().reverse().forEach(p => {
                    body.innerHTML += `
                        <tr>
                            <td class="p-2 text-gray-500">${p.tgl}</td>
                            <td class="p-2 font-medium">${p.ket}</td>
                            <td class="p-2 text-right font-bold text-orange-600">Rp ${p.nom.toLocaleString('id-ID')}</td>
                            <td class="p-2 text-center">
                                ${p.bukti ? `<button onclick="showBuktiModal('${p.bukti}', '${p.ket}')" class="text-[10px] bg-orange-100 text-orange-700 px-1 rounded">Foto</button>` : '—'}
                            </td>
                        </tr>
                    `;
                });
            }
        }

        function renderLaporanKasBody() {
            const body = document.getElementById('tabel-laporan-kas-body');
            body.innerHTML = '';
            const yearData = iuranData[selectedYear] || {};
            let currentKasSaldo = 0;

            bulanList.forEach(b => {
                let iuranBulan = 0;
                Object.keys(yearData).forEach(n => {
                    if(yearData[n][b] && yearData[n][b].nominal) {
                        iuranBulan += yearData[n][b].nominal;
                    }
                });

                currentKasSaldo += iuranBulan;

                body.innerHTML += `
                    <tr>
                        <td class="p-1.5 border font-semibold">${b}</td>
                        <td class="p-1.5 border text-right text-green-600 font-medium">Rp ${iuranBulan.toLocaleString('id-ID')}</td>
                        <td class="p-1.5 border text-right text-orange-600">-</td>
                        <td class="p-1.5 border text-right text-rose-600">-</td>
                        <td class="p-1.5 border text-right font-bold text-blue-600">Rp ${currentKasSaldo.toLocaleString('id-ID')}</td>
                    </tr>
                `;
            });
        }

        function switchKasSubTab(sub) {
            ['dana-sosial', 'pengeluaran', 'laporan-kas'].forEach(s => {
                document.getElementById(`kas-sub-${s}`).classList.add('hidden');
                const btn = document.getElementById(`subnav-${s}`);
                btn.className = "flex-1 py-2 rounded-lg text-center text-gray-600";
            });

            document.getElementById(`kas-sub-${sub}`).classList.remove('hidden');
            const activeBtn = document.getElementById(`subnav-${sub}`);
            activeBtn.className = "flex-1 py-2 rounded-lg text-center bg-blue-600 text-white font-bold";
        }

        function switchTab(tabName) {
            ['beranda', 'iuran', 'lapor', 'kas', 'kegiatan'].forEach(tab => {
                document.getElementById(`tab-${tab}`).classList.add('hidden');
                const btn = document.getElementById(`btn-${tab}`);
                if(btn) btn.classList.remove('active-tab');
            });

            document.getElementById(`tab-${tabName}`).classList.remove('hidden');
            const activeBtn = document.getElementById(`btn-${tabName}`);
            if(activeBtn && tabName !== 'lapor') activeBtn.classList.add('active-tab');
        }
    </script>
</body>
</html>
