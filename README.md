
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
        .sticky-col-no {
            position: sticky;
            left: 0;
            background-color: white;
            z-index: 10;
        }
        .sticky-col-nama {
            position: sticky;
            left: 35px;
            background-color: white;
            z-index: 10;
        }
        th.sticky-col-no, th.sticky-col-nama {
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
            <div class="w-24 h-24 bg-yellow-400 rounded-full flex items-center justify-center mx-auto mb-3 shadow-lg border-4 border-white text-blue-900 font-black text-3xl tracking-tighter">
                <i class="fas fa-users text-4xl text-blue-900"></i>
            </div>
            <h1 class="text-2xl font-bold">Keluarga Besar Wheel</h1>
            <p class="text-sm opacity-80">Family Gathering Wheel Section</p>
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

    <!-- MODAL BUKTI TRANSFER & FOTO KEGIATAN -->
    <div id="modal-bukti" class="fixed inset-0 bg-black/70 z-50 flex items-center justify-center p-4 hidden">
        <div class="bg-white w-full max-w-sm rounded-2xl p-4 shadow-xl relative">
            <button onclick="closeModalBukti()" class="absolute top-3 right-3 text-gray-500 hover:text-gray-800 font-bold text-lg">&times;</button>
            <h3 class="font-bold text-gray-800 text-sm mb-3"><i class="fas fa-image mr-1 text-blue-600"></i> Detail Gambar</h3>
            <div class="p-2 border rounded-xl bg-gray-50 text-center">
                <img id="img-bukti-target" src="" alt="Bukti / Foto" class="max-h-96 mx-auto rounded-lg object-contain">
                <p id="txt-bukti-ket" class="text-xs text-gray-600 mt-2 font-medium"></p>
            </div>
        </div>
    </div>

    <!-- MAIN APP -->
    <div id="app" class="max-w-md mx-auto bg-gray-50 min-h-screen shadow-md relative hidden">
        
        <!-- HEADER -->
        <div class="bg-blue-600 text-white p-4 rounded-b-2xl shadow-lg">
            <div class="flex justify-between items-center mb-2">
                <div class="flex items-center space-x-3">
                    <div class="w-10 h-10 bg-yellow-400 rounded-full flex items-center justify-center border-2 border-white text-blue-900 font-bold">
                        <i class="fas fa-users text-lg"></i>
                    </div>
                    <div>
                        <h1 class="text-lg font-bold leading-tight">Keluarga Besar Wheel</h1>
                        <p class="text-[10px] text-blue-200">Family Gathering Wheel Section</p>
                    </div>
                </div>
                <div class="flex space-x-1">
                    <button onclick="logout()" title="Keluar" class="bg-red-500 p-2 rounded-lg text-xs hover:bg-red-600"><i class="fas fa-sign-out-alt"></i></button>
                </div>
            </div>
            <div class="flex justify-between items-center text-xs text-blue-100 mt-2">
                <p>Status: <b id="user-role-label" class="capitalize">Anggota</b></p>
                <div class="flex items-center space-x-1">
                    <span>Tahun:</span>
                    <select id="filter-tahun" onchange="updateAllData()" class="bg-blue-700 text-white text-xs rounded px-2 py-1 border border-blue-400 font-bold">
                        <!-- Dynamic Years -->
                    </select>
                </div>
            </div>
        </div>

        <!-- TAB: BERANDA -->
        <div id="tab-beranda" class="p-4 space-y-4">
            <div class="bg-emerald-600 text-white p-4 rounded-2xl flex justify-between items-center shadow">
                <div>
                    <p class="text-xs opacity-90">Saldo Kas Saat Ini (<span class="lbl-tahun">2026</span>)</p>
                    <p class="text-2xl font-bold" id="total-saldo-display">Rp 0</p>
                </div>
                <div class="w-10 h-10 bg-yellow-400 text-yellow-900 rounded-full flex items-center justify-center font-bold text-xl">$</div>
            </div>

            <!-- Panel Konfirmasi Laporan Khusus Admin -->
            <div id="admin-approval-panel" class="bg-amber-50 border border-amber-200 rounded-2xl p-3 hidden">
                <h3 class="text-xs font-bold text-amber-800 mb-2"><i class="fas fa-user-shield mr-1"></i> Panel Admin: Persetujuan Laporan</h3>
                <div id="admin-laporan-list" class="space-y-2">
                    <!-- Dynamic List -->
                </div>
            </div>

            <!-- Grid Summary -->
            <div class="grid grid-cols-2 gap-3">
                <div class="bg-blue-600 text-white p-3 rounded-2xl">
                    <p class="text-xs">Bayar Bulan Ini (<span id="lbl-bulan-ini">Jul</span>)</p>
                    <p class="text-xl font-bold mt-1" id="stat-bayar-bulan">0 / 0 <span class="text-xs font-normal">anggota</span></p>
                </div>
                <div class="bg-purple-600 text-white p-3 rounded-2xl">
                    <p class="text-xs">Iuran Bulan Ini (<span id="lbl-bulan-ini-2">Jul</span>)</p>
                    <p class="text-xl font-bold mt-1" id="stat-iuran-bulan">Rp 0</p>
                </div>
                <div class="bg-sky-500 text-white p-3 rounded-2xl">
                    <p class="text-xs">Total Iuran Masuk (<span class="lbl-tahun">2026</span>)</p>
                    <p class="text-xl font-bold mt-1" id="stat-total-iuran">Rp 0</p>
                </div>
                <div class="bg-orange-500 text-white p-3 rounded-2xl">
                    <p class="text-xs">Total Pengeluaran (<span class="lbl-tahun">2026</span>)</p>
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
                <h2 class="text-lg font-bold">Laporan Iuran Anggota</h2>
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

            <!-- Tabel Iuran -->
            <div class="overflow-x-auto bg-white rounded-xl shadow border">
                <table class="w-full text-left text-xs border-collapse">
                    <thead class="bg-blue-600 text-white">
                        <tr id="tabel-iuran-header">
                            <th class="p-2 border-b text-center sticky-col-no w-8">No</th>
                            <th class="p-2 border-b sticky-col-nama min-w-[120px]">Nama</th>
                            <th class="p-2 border-b text-center min-w-[45px]">Jan</th>
                            <th class="p-2 border-b text-center min-w-[45px]">Feb</th>
                            <th class="p-2 border-b text-center min-w-[45px]">Mar</th>
                            <th class="p-2 border-b text-center min-w-[45px]">Apr</th>
                            <th class="p-2 border-b text-center min-w-[45px]">Mei</th>
                            <th class="p-2 border-b text-center min-w-[45px]">Jun</th>
                            <th class="p-2 border-b text-center min-w-[45px]">Jul</th>
                            <th class="p-2 border-b text-center min-w-[45px]">Agu</th>
                            <th class="p-2 border-b text-center min-w-[45px]">Sep</th>
                            <th class="p-2 border-b text-center min-w-[45px]">Okt</th>
                            <th class="p-2 border-b text-center min-w-[45px]">Nov</th>
                            <th class="p-2 border-b text-center min-w-[45px]">Des</th>
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
                <div>
                    <label class="text-xs font-semibold text-gray-600">Nominal Per Bulan (Rp)</label>
                    <input type="number" id="input-nominal" value="50000" required class="w-full p-2 border rounded-lg text-sm">
                </div>
                
                <div>
                    <label class="text-xs font-semibold text-gray-600 mb-1 block">Pilih Bulan (Tahun <span class="lbl-tahun">2026</span>):</label>
                    <div id="container-checkbox-bulan" class="grid grid-cols-3 gap-2 bg-gray-50 p-2.5 rounded-lg border max-h-40 overflow-y-auto">
                        <!-- Dynamic Checkboxes -->
                    </div>
                </div>

                <div>
                    <label class="text-xs font-semibold text-gray-600">Upload Bukti Transfer</label>
                    <input type="file" id="input-file-bukti" accept="image/*" required class="w-full text-xs p-2 border rounded-lg">
                </div>
                <button type="submit" class="w-full bg-blue-600 text-white py-2.5 rounded-lg font-bold text-xs uppercase tracking-wide">Kirim Laporan Ke Admin</button>
            </form>
        </div>

        <!-- TAB: KAS MENU -->
        <div id="tab-kas" class="p-4 space-y-4 hidden">
            <div class="flex bg-white rounded-xl p-1 shadow border text-xs font-bold">
                <button onclick="switchKasSubTab('dana-sosial')" id="subnav-dana-sosial" class="flex-1 py-2 rounded-lg text-center bg-blue-600 text-white">❤️ Dana Sosial</button>
                <button onclick="switchKasSubTab('pengeluaran')" id="subnav-pengeluaran" class="flex-1 py-2 rounded-lg text-center text-gray-600">💸 Pengeluaran</button>
                <button onclick="switchKasSubTab('laporan-kas')" id="subnav-laporan-kas" class="flex-1 py-2 rounded-lg text-center text-gray-600">📊 Laporan Kas</button>
            </div>

            <!-- SUB TAB 1: DANA SOSIAL -->
            <div id="kas-sub-dana-sosial" class="space-y-3">
                <div class="bg-rose-50 border border-rose-200 p-3 rounded-xl flex justify-between items-center">
                    <div>
                        <p class="text-xs text-rose-700 font-semibold">Total Dana Sosial (<span class="lbl-tahun">2026</span>)</p>
                        <p class="text-xl font-bold text-rose-800" id="total-dana-sosial-txt">Rp 0</p>
                    </div>
                    <i class="fas fa-heart text-2xl text-rose-500"></i>
                </div>

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
                        <p class="text-xs text-orange-700 font-semibold">Total Pengeluaran Operasional (<span class="lbl-tahun">2026</span>)</p>
                        <p class="text-xl font-bold text-orange-800" id="total-pengeluaran-txt">Rp 0</p>
                    </div>
                    <i class="fas fa-wallet text-2xl text-orange-500"></i>
                </div>

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
                    <h3 class="font-bold text-sm text-gray-800 mb-2"><i class="fas fa-chart-line text-blue-600 mr-1"></i> Arus Kas Bulanan (<span class="lbl-tahun">2026</span>)</h3>
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

        <!-- TAB: KEGIATAN ANGGOTA -->
        <div id="tab-kegiatan" class="p-4 hidden space-y-4">
            <h2 class="text-lg font-bold text-gray-800">Kegiatan Anggota (<span class="lbl-tahun">2026</span>)</h2>

            <div class="bg-white p-3 rounded-xl shadow border space-y-2">
                <p class="text-xs font-bold text-blue-600"><i class="fas fa-plus-circle mr-1"></i> Upload Kegiatan Baru</p>
                <input type="text" id="input-judul-kegiatan" placeholder="Judul Kegiatan..." class="w-full p-2 border rounded-lg text-xs">
                <input type="date" id="input-tgl-kegiatan" class="w-full p-2 border rounded-lg text-xs">
                <textarea id="input-desc-kegiatan" placeholder="Deskripsi atau Notulensi Kegiatan..." class="w-full p-2 border rounded-lg text-xs rows-2"></textarea>
                <div>
                    <label class="block text-[10px] text-gray-500 mb-1">Foto Kegiatan (Opsional)</label>
                    <input type="file" id="input-foto-kegiatan" accept="image/*" class="w-full text-xs p-1 border rounded-lg">
                </div>
                <button onclick="tambahKegiatan()" class="w-full bg-blue-600 text-white py-2 rounded-lg font-bold text-xs">Simpan & Publikasikan</button>
            </div>

            <div class="space-y-3">
                <p class="text-xs font-bold text-gray-600"><i class="fas fa-history mr-1"></i> Riwayat Kegiatan (<span class="lbl-tahun">2026</span>)</p>
                <div id="list-riwayat-kegiatan" class="space-y-3">
                    <!-- Dynamic List -->
                </div>
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

        let anggotaList = JSON.parse(localStorage.getItem('anggotaList')) || defaultAnggota;
        let passAnggota = 'wheel123';
        let passAdmin = localStorage.getItem('passAdmin') || 'Mo12345';
        let currentRole = '';

        let iuranData = JSON.parse(localStorage.getItem('iuranData')) || {}; 
        let laporanPending = JSON.parse(localStorage.getItem('laporanPending')) || [];
        let pengeluaranList = JSON.parse(localStorage.getItem('pengeluaranList')) || [];
        let danaSosialList = JSON.parse(localStorage.getItem('danaSosialList')) || [];
        
        let kegiatanList = JSON.parse(localStorage.getItem('kegiatanList')) || [
            {
                id: 1,
                judul: "Silaturahmi & Gathering",
                tgl: "2026-07-01",
                desc: "Dokumentasi kegiatan dan notulensi rapat warga diperbarui secara berkala.",
                foto: ""
            }
        ];

        const currentYear = new Date().getFullYear();
        let selectedYear = currentYear;

        function initYearsAndMonths() {
            const selectFilter = document.getElementById('filter-tahun');
            selectFilter.innerHTML = '';
            for(let y = currentYear - 2; y <= 2030; y++) {
                selectFilter.innerHTML += `<option value="${y}" ${y === currentYear ? 'selected' : ''}>${y}</option>`;
            }
            document.querySelectorAll('.current-year-txt').forEach(el => el.innerText = currentYear);

            const boxContainer = document.getElementById('container-checkbox-bulan');
            boxContainer.innerHTML = '';
            bulanList.forEach(b => {
                boxContainer.innerHTML += `
                    <label class="flex items-center space-x-1.5 text-xs text-gray-700 font-medium cursor-pointer">
                        <input type="checkbox" name="bulan_lapor" value="${b}" class="rounded text-blue-600 focus:ring-blue-500">
                        <span>${b}</span>
                    </label>
                `;
            });

            document.getElementById('input-tgl-kegiatan').valueAsDate = new Date();
        }

        function login() {
            const role = document.getElementById('role-select').value;
            const inputPw = document.getElementById('password-input').value;

            if (role === 'anggota' && inputPw === passAnggota) {
                currentRole = 'anggota';
            } else if (role === 'admin' && inputPw === passAdmin) {
                currentRole = 'admin';
            } else {
                alert('Password yang Anda masukkan salah!');
                return;
            }

            document.getElementById('login-screen').classList.add('hidden');
            document.getElementById('app').classList.remove('hidden');
            document.getElementById('user-role-label').innerText = currentRole;
            
            initYearsAndMonths();
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
            const year = document.getElementById('filter-tahun').value;
            const fileInput = document.getElementById('input-file-bukti');

            const checkboxes = document.querySelectorAll('input[name="bulan_lapor"]:checked');
            const selectedBulans = Array.from(checkboxes).map(cb => cb.value);

            if(selectedBulans.length === 0) {
                return alert('Pilih minimal satu bulan yang dibayar!');
            }

            if(fileInput.files && fileInput.files[0]) {
                const reader = new FileReader();
                reader.onload = function(e) {
                    const buktiBase64 = e.target.result;
                    
                    selectedBulans.forEach(bln => {
                        const laporanBaru = { id: Date.now() + Math.random(), nama, nominal, bulan: bln, tahun: year, bukti: buktiBase64 };
                        laporanPending.push(laporanBaru);
                    });

                    localStorage.setItem('laporanPending', JSON.stringify(laporanPending));
                    
                    alert(`Laporan pembayaran ${selectedBulans.length} bulan berhasil dikirim ke Admin!`);
                    fileInput.value = '';
                    checkboxes.forEach(cb => cb.checked = false);
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
                alert('Laporan pending berhasil dihapus!');
                updateAllData();
            }
        }

        function hapusIuranSelesai(tahun, nama, bulan) {
            if (confirm(`Hapus data iuran ${nama} untuk bulan ${bulan} ${tahun}?`)) {
                if (iuranData[tahun] && iuranData[tahun][nama] && iuranData[tahun][nama][bulan]) {
                    delete iuranData[tahun][nama][bulan];
                    localStorage.setItem('iuranData', JSON.stringify(iuranData));
                    alert('Data iuran berhasil dihapus!');
                    updateAllData();
                }
            }
        }

        function showBukti(src, ket) {
            document.getElementById('img-bukti-target').src = src;
            document.getElementById('txt-bukti-ket').innerText = ket || '';
            document.getElementById('modal-bukti').classList.remove('hidden');
        }

        function closeModalBukti() {
            document.getElementById('modal-bukti').classList.add('hidden');
        }

        function switchTab(tab) {
            ['beranda', 'iuran', 'lapor', 'kas', 'kegiatan'].forEach(t => {
                document.getElementById(`tab-${t}`).classList.add('hidden');
                document.getElementById(`btn-${t}`).classList.remove('active-tab');
            });

            document.getElementById(`tab-${tab}`).classList.remove('hidden');
            document.getElementById(`btn-${tab}`).classList.add('active-tab');
        }

        function switchKasSubTab(sub) {
            ['dana-sosial', 'pengeluaran', 'laporan-kas'].forEach(s => {
                document.getElementById(`kas-sub-${s}`).classList.add('hidden');
                const btn = document.getElementById(`subnav-${s}`);
                btn.classList.remove('bg-blue-600', 'text-white');
                btn.classList.add('text-gray-600');
            });

            document.getElementById(`kas-sub-${sub}`).classList.remove('hidden');
            const activeBtn = document.getElementById(`subnav-${sub}`);
            activeBtn.classList.add('bg-blue-600', 'text-white');
            activeBtn.classList.remove('text-gray-600');
        }

        function tambahDanaSosial() {
            const kategori = document.getElementById('input-kategori-dansos').value;
            const nominal = parseInt(document.getElementById('input-nominal-dansos').value);
            const ket = document.getElementById('input-ket-dansos').value.trim();
            const year = document.getElementById('filter-tahun').value;

            if(!nominal || !ket) return alert('Lengkapi data dana sosial!');

            const baru = {
                id: Date.now(),
                tgl: new Date().toISOString().split('T')[0],
                kategori,
                nominal,
                ket,
                tahun: year
            };

            danaSosialList.push(baru);
            localStorage.setItem('danaSosialList', JSON.stringify(danaSosialList));
            
            document.getElementById('input-nominal-dansos').value = '';
            document.getElementById('input-ket-dansos').value = '';
            
            alert('Dana sosial berhasil disimpan!');
            updateAllData();
        }

        function tambahPengeluaran() {
            const ket = document.getElementById('input-ket-pengeluaran').value.trim();
            const nominal = parseInt(document.getElementById('input-nominal-pengeluaran').value);
            const fileInput = document.getElementById('input-bukti-pengeluaran');
            const year = document.getElementById('filter-tahun').value;

            if(!nominal || !ket) return alert('Lengkapi data pengeluaran!');

            const saveProcess = (buktiBase64 = "") => {
                const baru = {
                    id: Date.now(),
                    tgl: new Date().toISOString().split('T')[0],
                    ket,
                    nominal,
                    bukti: buktiBase64,
                    tahun: year
                };

                pengeluaranList.push(baru);
                localStorage.setItem('pengeluaranList', JSON.stringify(pengeluaranList));

                document.getElementById('input-ket-pengeluaran').value = '';
                document.getElementById('input-nominal-pengeluaran').value = '';
                fileInput.value = '';

                alert('Pengeluaran berhasil disimpan!');
                updateAllData();
            };

            if(fileInput.files && fileInput.files[0]) {
                const reader = new FileReader();
                reader.onload = e => saveProcess(e.target.result);
                reader.readAsDataURL(fileInput.files[0]);
            } else {
                saveProcess();
            }
        }

        function tambahKegiatan() {
            const judul = document.getElementById('input-judul-kegiatan').value.trim();
            const tgl = document.getElementById('input-tgl-kegiatan').value;
            const desc = document.getElementById('input-desc-kegiatan').value.trim();
            const fileInput = document.getElementById('input-foto-kegiatan');

            if(!judul || !tgl || !desc) return alert('Lengkapi detail kegiatan!');

            const saveKegiatan = (fotoBase64 = "") => {
                const newKegiatan = {
                    id: Date.now(),
                    judul,
                    tgl,
                    desc,
                    foto: fotoBase64
                };

                kegiatanList.unshift(newKegiatan);
                localStorage.setItem('kegiatanList', JSON.stringify(kegiatanList));

                document.getElementById('input-judul-kegiatan').value = '';
                document.getElementById('input-desc-kegiatan').value = '';
                fileInput.value = '';

                alert('Kegiatan berhasil ditambahkan!');
                updateAllData();
            };

            if(fileInput.files && fileInput.files[0]) {
                const reader = new FileReader();
                reader.onload = e => saveKegiatan(e.target.result);
                reader.readAsDataURL(fileInput.files[0]);
            } else {
                saveKegiatan();
            }
        }

        function updateAllData() {
            selectedYear = document.getElementById('filter-tahun').value;
            
            // Perbarui teks tahun di UI
            document.querySelectorAll('.lbl-tahun').forEach(el => el.innerText = selectedYear);

            // Render Select Nama Lapor
            const selectNama = document.getElementById('input-nama');
            selectNama.innerHTML = '';
            anggotaList.forEach(nama => {
                selectNama.innerHTML += `<option value="${nama}">${nama}</option>`;
            });

            // Toggle Tampilan Admin/Anggota
            if(currentRole === 'admin') {
                document.getElementById('admin-approval-panel').classList.remove('hidden');
                document.getElementById('admin-tambah-anggota-form').classList.remove('hidden');
                document.getElementById('admin-dansos-form').classList.remove('hidden');
                document.getElementById('admin-pengeluaran-form').classList.remove('hidden');
            } else {
                document.getElementById('admin-approval-panel').classList.add('hidden');
                document.getElementById('admin-tambah-anggota-form').classList.add('hidden');
                document.getElementById('admin-dansos-form').classList.add('hidden');
                document.getElementById('admin-pengeluaran-form').classList.add('hidden');
            }

            // Render Laporan Pending (Admin Approval)
            const listPending = document.getElementById('admin-laporan-list');
            listPending.innerHTML = '';
            const filteredPending = laporanPending.filter(l => l.tahun == selectedYear);

            if(filteredPending.length === 0) {
                listPending.innerHTML = `<p class="text-xs text-amber-700 italic">Tidak ada laporan pending di tahun ${selectedYear}.</p>`;
            } else {
                filteredPending.forEach(item => {
                    listPending.innerHTML += `
                        <div class="bg-white p-2.5 rounded-xl border flex justify-between items-center text-xs">
                            <div>
                                <p class="font-bold text-gray-800">${item.nama} <span class="text-blue-600">(${item.bulan} ${item.tahun})</span></p>
                                <p class="text-gray-500 font-medium">Rp ${item.nominal.toLocaleString()}</p>
                                <button onclick="showBukti('${item.bukti}', 'Bukti Bayar: ${item.nama} (${item.bulan})')" class="text-[10px] text-blue-600 font-bold underline mt-0.5">Lihat Bukti</button>
                            </div>
                            <div class="flex space-x-1">
                                <button onclick="setujuiLaporan(${item.id})" class="bg-emerald-600 text-white px-2.5 py-1 rounded-lg text-xs font-bold hover:bg-emerald-700"><i class="fas fa-check mr-1"></i> Setuju</button>
                                <button onclick="hapusLaporan(${item.id})" class="bg-red-500 text-white px-2 py-1 rounded-lg text-xs font-bold hover:bg-red-600"><i class="fas fa-trash"></i></button>
                            </div>
                        </div>
                    `;
                });
            }

            // Render Tabel Iuran
            document.getElementById('total-anggota-count').innerText = `${anggotaList.length} Anggota`;
            const bodyTable = document.getElementById('tabel-iuran-body');
            bodyTable.innerHTML = '';

            let totalIuranTahunIni = 0;
            let targetMonthIdx = (selectedYear == currentYear) ? new Date().getMonth() : 0; 
            let targetMonthName = bulanList[targetMonthIdx];

            document.getElementById('lbl-bulan-ini').innerText = targetMonthName;
            document.getElementById('lbl-bulan-ini-2').innerText = targetMonthName;

            let countBayarBulanIni = 0;
            let totalIuranBulanIni = 0;

            const yearIuran = iuranData[selectedYear] || {};

            anggotaList.forEach((nama, idx) => {
                let rowHtml = `<tr>
                    <td class="p-2 border-b text-center text-gray-500 sticky-col-no">${idx + 1}</td>
                    <td class="p-2 border-b font-medium text-gray-800 sticky-col-nama">${nama}</td>`;

                bulanList.forEach((bln, mIdx) => {
                    const dataBayar = yearIuran[nama] ? yearIuran[nama][bln] : null;

                    if(dataBayar) {
                        totalIuranTahunIni += dataBayar.nominal;
                        if(mIdx === targetMonthIdx) {
                            countBayarBulanIni++;
                            totalIuranBulanIni += dataBayar.nominal;
                        }

                        let hapusBtn = currentRole === 'admin' ? `<button onclick="hapusIuranSelesai('${selectedYear}', '${nama}', '${bln}')" class="text-red-500 hover:text-red-700 ml-1" title="Hapus Laporan"><i class="fas fa-times-circle"></i></button>` : '';

                        rowHtml += `
                            <td class="p-2 border-b text-center">
                                <div class="flex items-center justify-center">
                                    <button onclick="showBukti('${dataBayar.bukti}', 'Bukti Bayar ${nama} (${bln})')" class="text-emerald-600 font-bold bg-emerald-50 px-1 rounded border border-emerald-200">
                                        ✓
                                    </button>
                                    ${hapusBtn}
                                </div>
                            </td>
                        `;
                    } else {
                        rowHtml += `<td class="p-2 border-b text-center text-gray-300">-</td>`;
                    }
                });

                rowHtml += `</tr>`;
                bodyTable.innerHTML += rowHtml;
            });

            // Summary Stats
            document.getElementById('stat-bayar-bulan').innerHTML = `${countBayarBulanIni} / ${anggotaList.length} <span class="text-xs font-normal">anggota</span>`;
            document.getElementById('stat-iuran-bulan').innerText = `Rp ${totalIuranBulanIni.toLocaleString()}`;
            document.getElementById('stat-total-iuran').innerText = `Rp ${totalIuranTahunIni.toLocaleString()}`;

            // Render Dana Sosial (Filtered Per Tahun)
            const dansosBody = document.getElementById('list-dana-sosial-body');
            dansosBody.innerHTML = '';
            let totalDansos = 0;

            const filteredDansos = danaSosialList.filter(d => {
                const yr = d.tahun || (d.tgl ? d.tgl.split('-')[0] : selectedYear);
                return yr == selectedYear;
            });

            filteredDansos.forEach(item => {
                totalDansos += item.nominal;
                dansosBody.innerHTML += `
                    <tr>
                        <td class="p-2 border-b text-gray-500">${item.tgl}</td>
                        <td class="p-2 border-b font-semibold text-rose-600">${item.kategori}</td>
                        <td class="p-2 border-b text-gray-700">${item.ket}</td>
                        <td class="p-2 border-b text-right font-bold text-gray-800">Rp ${item.nominal.toLocaleString()}</td>
                    </tr>
                `;
            });
            document.getElementById('total-dana-sosial-txt').innerText = `Rp ${totalDansos.toLocaleString()}`;

            // Render Pengeluaran (Filtered Per Tahun)
            const pengeluaranBody = document.getElementById('list-pengeluaran-body');
            pengeluaranBody.innerHTML = '';
            let totalPengeluaran = 0;

            const filteredPengeluaran = pengeluaranList.filter(p => {
                const yr = p.tahun || (p.tgl ? p.tgl.split('-')[0] : selectedYear);
                return yr == selectedYear;
            });

            filteredPengeluaran.forEach(item => {
                totalPengeluaran += item.nominal;
                const btnBukti = item.bukti ? `<button onclick="showBukti('${item.bukti}', '${item.ket}')" class="text-blue-600 underline text-[10px]">Lihat</button>` : '-';
                
                pengeluaranBody.innerHTML += `
                    <tr>
                        <td class="p-2 border-b text-gray-500">${item.tgl}</td>
                        <td class="p-2 border-b text-gray-800 font-medium">${item.ket}</td>
                        <td class="p-2 border-b text-right font-bold text-orange-600">Rp ${item.nominal.toLocaleString()}</td>
                        <td class="p-2 border-b text-center">${btnBukti}</td>
                    </tr>
                `;
            });
            document.getElementById('total-pengeluaran-txt').innerText = `Rp ${totalPengeluaran.toLocaleString()}`;
            document.getElementById('stat-total-pengeluaran').innerText = `Rp ${(totalPengeluaran + totalDansos).toLocaleString()}`;

            // Saldo Kas Murni Per Tahun
            const totalSaldoKas = totalIuranTahunIni - (totalPengeluaran + totalDansos);
            document.getElementById('total-saldo-display').innerText = `Rp ${totalSaldoKas.toLocaleString()}`;

            // Laporan Arus Kas Bulanan
            const kasBody = document.getElementById('tabel-laporan-kas-body');
            kasBody.innerHTML = '';
            let runningSaldo = 0;

            bulanList.forEach((bln, idx) => {
                let masukBln = 0;
                anggotaList.forEach(nama => {
                    if(yearIuran[nama] && yearIuran[nama][bln]) {
                        masukBln += yearIuran[nama][bln].nominal;
                    }
                });

                let keluarBln = filteredPengeluaran
                    .filter(p => p.tgl && new Date(p.tgl).getMonth() === idx)
                    .reduce((acc, curr) => acc + curr.nominal, 0);

                let dansosBln = filteredDansos
                    .filter(d => d.tgl && new Date(d.tgl).getMonth() === idx)
                    .reduce((acc, curr) => acc + curr.nominal, 0);

                runningSaldo += (masukBln - keluarBln - dansosBln);

                kasBody.innerHTML += `
                    <tr>
                        <td class="p-1.5 border font-semibold text-gray-700">${bln}</td>
                        <td class="p-1.5 border text-right text-emerald-600 font-medium">Rp ${masukBln.toLocaleString()}</td>
                        <td class="p-1.5 border text-right text-orange-600 font-medium">Rp ${keluarBln.toLocaleString()}</td>
                        <td class="p-1.5 border text-right text-rose-600 font-medium">Rp ${dansosBln.toLocaleString()}</td>
                        <td class="p-1.5 border text-right font-bold text-gray-800">Rp ${runningSaldo.toLocaleString()}</td>
                    </tr>
                `;
            });

            // Render Kegiatan (Filtered Per Tahun)
            const listKegiatan = document.getElementById('list-riwayat-kegiatan');
            listKegiatan.innerHTML = '';
            
            const filteredKegiatan = kegiatanList.filter(k => {
                if(!k.tgl) return true;
                return k.tgl.startsWith(selectedYear.toString());
            });

            if(filteredKegiatan.length === 0) {
                listKegiatan.innerHTML = `<p class="text-xs text-gray-500 italic text-center py-4">Belum ada kegiatan tercatat pada tahun ${selectedYear}.</p>`;
            } else {
                filteredKegiatan.forEach(keg => {
                    const imgTag = keg.foto ? `<img src="${keg.foto}" onclick="showBukti('${keg.foto}', '${keg.judul}')" class="w-full h-32 object-cover rounded-lg cursor-pointer mb-2">` : '';
                    listKegiatan.innerHTML += `
                        <div class="bg-white p-3 rounded-xl shadow border space-y-1">
                            ${imgTag}
                            <div class="flex justify-between items-center">
                                <h3 class="font-bold text-sm text-gray-800">${keg.judul}</h3>
                                <span class="text-[10px] text-gray-400"><i class="far fa-calendar-alt mr-1"></i>${keg.tgl}</span>
                            </div>
                            <p class="text-xs text-gray-600 leading-relaxed">${keg.desc}</p>
                        </div>
                    `;
                });
            }
        }
    </script>
</body>
</html>
