
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WheelCare - Aplikasi Kas & Kegiatan Warga</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome CDN -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        .sticky-col {
            position: sticky;
            left: 0;
            background-color: white;
            z-index: 10;
        }
        .active-tab {
            color: #2563eb;
            border-bottom: 2px solid #2563eb;
            font-weight: bold;
        }
    </style>
</head>
<body class="bg-gray-100 min-h-screen text-gray-800 pb-20">

    <!-- HEADER / NAVIGATION BAR -->
    <header class="bg-blue-600 text-white p-4 shadow-md sticky top-0 z-50">
        <div class="max-w-4xl mx-auto flex justify-between items-center">
            <div>
                <h1 class="text-xl font-bold flex items-center gap-2">
                    <i class="fas fa-dharmachakra"></i> WheelCare
                </h1>
                <p class="text-xs text-blue-100">Keluarga Besar Wheel</p>
            </div>
            
            <div class="flex items-center space-x-2">
                <!-- Select Tahun Filter -->
                <select id="filter-tahun" onchange="updateAllData()" class="bg-blue-700 text-white text-xs px-2 py-1 rounded border border-blue-500 font-medium">
                    <option value="2026" selected>2026</option>
                    <option value="2025">2025</option>
                </select>

                <!-- Select Role (Admin / Warga) -->
                <select id="role-selector" onchange="changeRole()" class="bg-white text-blue-900 text-xs px-2 py-1 rounded font-bold shadow">
                    <option value="admin">Mode: Admin</option>
                    <option value="warga">Mode: Warga</option>
                </select>
            </div>
        </div>
    </header>

    <!-- NAVIGATION TABS -->
    <nav class="bg-white border-b shadow-sm sticky top-[60px] z-40">
        <div class="max-w-4xl mx-auto flex justify-around text-xs font-medium text-gray-600">
            <button id="btn-beranda" onclick="switchTab('beranda')" class="py-3 px-2 active-tab"><i class="fas fa-home"></i> Beranda</button>
            <button id="btn-iuran" onclick="switchTab('iuran')" class="py-3 px-2"><i class="fas fa-table"></i> Iuran</button>
            <button id="btn-lapor" onclick="switchTab('lapor')" class="py-3 px-2"><i class="fas fa-paper-plane"></i> Lapor</button>
            <button id="btn-kas" onclick="switchTab('kas')" class="py-3 px-2"><i class="fas fa-wallet"></i> Kas</button>
            <button id="btn-kegiatan" onclick="switchTab('kegiatan')" class="py-3 px-2"><i class="fas fa-calendar-alt"></i> Kegiatan</button>
        </div>
    </nav>

    <!-- MAIN CONTENT CONTAINER -->
    <main class="max-w-4xl mx-auto p-4 space-y-4">

        <!-- TAB 1: BERANDA / DASHBOARD -->
        <section id="tab-beranda" class="space-y-4">
            <!-- Card Total Saldo -->
            <div class="bg-gradient-to-r from-blue-600 to-indigo-700 text-white rounded-2xl p-5 shadow-lg space-y-3">
                <p class="text-xs text-blue-100 font-medium uppercase tracking-wider">Total Saldo Kas Saat Ini</p>
                <h2 id="total-saldo-display" class="text-3xl font-extrabold">Rp 0</h2>
                <div class="pt-2 border-t border-blue-400/40 flex justify-between text-xs text-blue-100">
                    <span>Anggota: <b id="total-anggota-count" class="text-white">0</b></span>
                    <span>Status Kas: <b class="text-emerald-300">Aktif</b></span>
                </div>
            </div>

            <!-- Panel Approval Admin (Hanya Muncul di Mode Admin) -->
            <div id="admin-approval-panel" class="bg-amber-50 border border-amber-200 rounded-xl p-4 space-y-3">
                <div class="flex justify-between items-center">
                    <h3 class="font-bold text-amber-900 text-xs flex items-center gap-1.5">
                        <i class="fas fa-bell text-amber-600"></i> Persetujuan Pembayaran Iuran Pending
                    </h3>
                </div>
                <div id="admin-laporan-list" class="space-y-2">
                    <!-- Direset via JavaScript -->
                </div>
            </div>

            <!-- Ringskasan Statistik -->
            <div class="grid grid-cols-2 gap-3 text-xs">
                <div class="bg-white p-3 rounded-xl border shadow-sm space-y-1">
                    <span class="text-gray-500">Bayar Bulan Ini</span>
                    <p id="stat-bayar-bulan" class="font-bold text-base text-gray-800">0 / 0</p>
                </div>
                <div class="bg-white p-3 rounded-xl border shadow-sm space-y-1">
                    <span class="text-gray-500">Pemasukan Bulan Ini</span>
                    <p id="stat-iuran-bulan" class="font-bold text-base text-emerald-600">Rp 0</p>
                </div>
                <div class="bg-white p-3 rounded-xl border shadow-sm space-y-1">
                    <span class="text-gray-500">Total Iuran Masuk</span>
                    <p id="stat-total-iuran" class="font-bold text-base text-blue-600">Rp 0</p>
                </div>
                <div class="bg-white p-3 rounded-xl border shadow-sm space-y-1">
                    <span class="text-gray-500">Total Pengeluaran & Dansos</span>
                    <p id="stat-total-pengeluaran" class="font-bold text-base text-rose-600">Rp 0</p>
                </div>
            </div>

            <!-- Form Tambah Anggota (Admin Only) -->
            <div id="admin-tambah-anggota-form" class="bg-white p-4 rounded-xl border shadow-sm space-y-3">
                <h3 class="font-bold text-xs text-gray-800"><i class="fas fa-user-plus text-blue-600"></i> Tambah Anggota Warga Baru</h3>
                <div class="flex gap-2">
                    <input type="text" id="input-nama-anggota-baru" placeholder="Nama Lengkap Warga" class="flex-1 text-xs border p-2 rounded-lg focus:outline-blue-500">
                    <button onclick="tambahAnggota()" class="bg-blue-600 text-white text-xs px-3 py-2 rounded-lg font-bold hover:bg-blue-700">Tambah</button>
                </div>
            </div>
        </section>

        <!-- TAB 2: TABEL IURAN -->
        <section id="tab-iuran" class="hidden space-y-3">
            <div class="flex justify-between items-center">
                <h2 class="font-bold text-sm text-gray-800"><i class="fas fa-table text-blue-600"></i> Matriks Iuran Warga</h2>
            </div>
            
            <div class="overflow-x-auto bg-white rounded-xl border shadow-sm">
                <table class="w-full text-left text-xs border-collapse">
                    <thead class="bg-gray-50 text-gray-600 font-semibold">
                        <tr id="tabel-iuran-header">
                            <!-- Header Direset via JS -->
                        </tr>
                    </thead>
                    <tbody id="tabel-iuran-body" class="divide-y text-gray-700">
                        <!-- Content Direset via JS -->
                    </tbody>
                </table>
            </div>
        </section>

        <!-- TAB 3: LAPOR PEMBAYARAN -->
        <section id="tab-lapor" class="hidden space-y-4">
            <div class="bg-white p-4 rounded-xl border shadow-sm space-y-3">
                <h2 class="font-bold text-sm text-gray-800"><i class="fas fa-paper-plane text-blue-600"></i> Form Laporan Pembayaran Iuran</h2>
                <p class="text-xs text-gray-500">Kirim konfirmasi pembayaran iuran Anda agar diverifikasi oleh pengurus/admin.</p>

                <div class="space-y-3 text-xs">
                    <div>
                        <label class="block font-semibold mb-1">Pilih Nama Warga:</label>
                        <select id="input-nama" class="w-full border p-2 rounded-lg bg-gray-50 focus:outline-blue-500"></select>
                    </div>

                    <div>
                        <label class="block font-semibold mb-1">Pilih Bulan:</label>
                        <select id="input-bulan" class="w-full border p-2 rounded-lg bg-gray-50 focus:outline-blue-500">
                            <option value="Januari">Januari</option>
                            <option value="Februari">Februari</option>
                            <option value="Maret">Maret</option>
                            <option value="April">April</option>
                            <option value="Mei">Mei</option>
                            <option value="Juni">Juni</option>
                            <option value="Juli">Juli</option>
                            <option value="Agustus">Agustus</option>
                            <option value="September">September</option>
                            <option value="Oktober">Oktober</option>
                            <option value="November">November</option>
                            <option value="Desember">Desember</option>
                        </select>
                    </div>

                    <div>
                        <label class="block font-semibold mb-1">Nominal (Rp):</label>
                        <input type="number" id="input-nominal" value="50000" class="w-full border p-2 rounded-lg focus:outline-blue-500">
                    </div>

                    <div>
                        <label class="block font-semibold mb-1">Bukti Transfer (Foto/Screenshot):</label>
                        <input type="file" id="input-bukti" accept="image/*" class="w-full border p-1.5 rounded-lg text-gray-500 bg-gray-50">
                    </div>

                    <button onclick="kirimLaporan()" class="w-full bg-emerald-600 text-white py-2.5 rounded-lg font-bold text-xs hover:bg-emerald-700 shadow">
                        <i class="fas fa-paper-plane"></i> Kirim Laporan Pembayaran
                    </button>
                </div>
            </div>
        </section>

        <!-- TAB 4: MANAJEMEN KAS -->
        <section id="tab-kas" class="hidden space-y-4">
            <!-- Sub Navigation Kas -->
            <div class="flex bg-gray-200 p-1 rounded-xl text-xs font-medium">
                <button id="subnav-dana-sosial" onclick="switchKasSubTab('dana-sosial')" class="flex-1 py-1.5 rounded-lg bg-blue-600 text-white font-bold">Dana Sosial</button>
                <button id="subnav-pengeluaran" onclick="switchKasSubTab('pengeluaran')" class="flex-1 py-1.5 rounded-lg text-gray-600">Pengeluaran</button>
                <button id="subnav-laporan-kas" onclick="switchKasSubTab('laporan-kas')" class="flex-1 py-1.5 rounded-lg text-gray-600">Arus Kas</button>
            </div>

            <!-- SubTab: Dana Sosial -->
            <div id="kas-sub-dana-sosial" class="space-y-4">
                <div id="admin-dansos-form" class="bg-white p-4 rounded-xl border shadow-sm space-y-3">
                    <h3 class="font-bold text-xs text-gray-800"><i class="fas fa-hand-holding-heart text-rose-500"></i> Catat Dana Sosial (Penerimaan)</h3>
                    <div class="space-y-2 text-xs">
                        <select id="input-kat-dansos" class="w-full border p-2 rounded-lg bg-gray-50">
                            <option value="Suka">Suka (Pernikahan, Kelahiran)</option>
                            <option value="Duka">Duka (Musibah, Sakit, Meninggal)</option>
                            <option value="Sumbangan">Sumbangan Sukarela</option>
                        </select>
                        <input type="number" id="input-nominal-dansos" placeholder="Nominal (Rp)" class="w-full border p-2 rounded-lg">
                        <input type="text" id="input-ket-dansos" placeholder="Keterangan (misal: Santunan Duka Almarhum A)" class="w-full border p-2 rounded-lg">
                        <button onclick="tambahDanaSosial()" class="w-full bg-rose-600 text-white py-2 rounded-lg font-bold hover:bg-rose-700">Simpan Dana Sosial</button>
                    </div>
                </div>

                <div class="bg-white rounded-xl border shadow-sm p-4 space-y-2">
                    <div class="flex justify-between items-center border-b pb-2">
                        <h3 class="font-bold text-xs text-gray-800">Riwayat Dana Sosial</h3>
                        <span id="total-dana-sosial-txt" class="font-bold text-xs text-rose-600">Rp 0</span>
                    </div>
                    <div class="overflow-x-auto">
                        <table class="w-full text-left text-xs">
                            <thead class="bg-gray-50 text-gray-600">
                                <tr>
                                    <th class="p-2 border-b">Tgl</th>
                                    <th class="p-2 border-b">Kat</th>
                                    <th class="p-2 border-b">Keterangan</th>
                                    <th class="p-2 border-b text-right">Nominal</th>
                                </tr>
                            </thead>
                            <tbody id="list-dana-sosial-body" class="divide-y"></tbody>
                        </table>
                    </div>
                </div>
            </div>

            <!-- SubTab: Pengeluaran Kas -->
            <div id="kas-sub-pengeluaran" class="hidden space-y-4">
                <div id="admin-pengeluaran-form" class="bg-white p-4 rounded-xl border shadow-sm space-y-3">
                    <h3 class="font-bold text-xs text-gray-800"><i class="fas fa-minus-circle text-orange-500"></i> Catat Pengeluaran Kas</h3>
                    <div class="space-y-2 text-xs">
                        <input type="text" id="input-ket-pengeluaran" placeholder="Keterangan Pengeluaran" class="w-full border p-2 rounded-lg">
                        <input type="number" id="input-nominal-pengeluaran" placeholder="Nominal (Rp)" class="w-full border p-2 rounded-lg">
                        <input type="file" id="input-bukti-pengeluaran" accept="image/*" class="w-full border p-1.5 rounded-lg text-gray-500 bg-gray-50">
                        <button onclick="tambahPengeluaran()" class="w-full bg-orange-600 text-white py-2 rounded-lg font-bold hover:bg-orange-700">Simpan Pengeluaran</button>
                    </div>
                </div>

                <div class="bg-white rounded-xl border shadow-sm p-4 space-y-2">
                    <div class="flex justify-between items-center border-b pb-2">
                        <h3 class="font-bold text-xs text-gray-800">Riwayat Pengeluaran Operasional</h3>
                        <span id="total-pengeluaran-txt" class="font-bold text-xs text-orange-600">Rp 0</span>
                    </div>
                    <div class="overflow-x-auto">
                        <table class="w-full text-left text-xs">
                            <thead class="bg-gray-50 text-gray-600">
                                <tr>
                                    <th class="p-2 border-b">Tgl</th>
                                    <th class="p-2 border-b">Keterangan</th>
                                    <th class="p-2 border-b text-right">Nominal</th>
                                    <th class="p-2 border-b text-center">Nota</th>
                                </tr>
                            </thead>
                            <tbody id="list-pengeluaran-body" class="divide-y"></tbody>
                        </table>
                    </div>
                </div>
            </div>

            <!-- SubTab: Laporan Arus Kas Bulanan -->
            <div id="kas-sub-laporan-kas" class="hidden space-y-4">
                <div class="bg-white rounded-xl border shadow-sm p-4 space-y-3">
                    <h3 class="font-bold text-xs text-gray-800"><i class="fas fa-chart-line text-blue-600"></i> Laporan Arus Kas Bulanan</h3>
                    <div class="overflow-x-auto">
                        <table class="w-full text-left text-xs border">
                            <thead class="bg-gray-100 text-gray-700 font-bold">
                                <tr>
                                    <th class="p-2 border">Bulan</th>
                                    <th class="p-2 border text-right">Masuk</th>
                                    <th class="p-2 border text-right">Keluar</th>
                                    <th class="p-2 border text-right">Dansos</th>
                                    <th class="p-2 border text-right">Saldo Akhir</th>
                                </tr>
                            </thead>
                            <tbody id="tabel-laporan-kas-body" class="divide-y"></tbody>
                        </table>
                    </div>
                </div>
            </div>
        </section>

        <!-- TAB 5: KEGIATAN WARGA -->
        <section id="tab-kegiatan" class="hidden space-y-4">
            <div class="bg-white p-4 rounded-xl border shadow-sm space-y-3">
                <h2 class="font-bold text-sm text-gray-800"><i class="fas fa-calendar-alt text-blue-600"></i> Agenda & Dokumentasi Kegiatan</h2>
                
                <div class="space-y-2 text-xs border-b pb-4">
                    <input type="text" id="input-judul-kegiatan" placeholder="Judul Kegiatan (Contoh: Wheel Gathering 2026)" class="w-full border p-2 rounded-lg">
                    <input type="date" id="input-tgl-kegiatan" class="w-full border p-2 rounded-lg bg-gray-50">
                    <textarea id="input-desc-kegiatan" placeholder="Deskripsi/Detail Acara" class="w-full border p-2 rounded-lg h-20"></textarea>
                    <input type="file" id="input-foto-kegiatan" accept="image/*" class="w-full border p-1.5 rounded-lg text-gray-500 bg-gray-50">
                    <button onclick="tambahKegiatan()" class="w-full bg-blue-600 text-white py-2 rounded-lg font-bold hover:bg-blue-700">Tambah Kegiatan</button>
                </div>

                <div id="list-riwayat-kegiatan" class="space-y-3 pt-2">
                    <!-- Dynamic List Kegiatan -->
                </div>
            </div>
        </section>

    </main>

    <!-- MODAL PRATINJAU FOTO BUKTI -->
    <div id="modal-bukti" class="fixed inset-0 bg-black/70 hidden z-50 flex items-center justify-center p-4">
        <div class="bg-white rounded-2xl max-w-sm w-full p-4 space-y-3 text-center relative">
            <button onclick="closeModalBukti()" class="absolute top-2 right-3 text-gray-500 text-xl font-bold">&times;</button>
            <h3 class="font-bold text-xs text-gray-800">Lampiran Bukti / Foto</h3>
            <div class="max-h-80 overflow-auto rounded-lg border bg-gray-50">
                <img id="img-bukti-target" src="" alt="Bukti Lampiran" class="w-full object-contain">
            </div>
            <p id="txt-bukti-ket" class="text-xs text-gray-600 font-medium"></p>
            <button onclick="closeModalBukti()" class="w-full bg-gray-800 text-white py-2 rounded-lg text-xs font-bold">Tutup</button>
        </div>
    </div>

    <!-- LOGIKA JAVASCRIPT APLIKASI -->
    <script>
        // --- 1. DATA INITIALIZATION & LOCALSTORAGE ---
        const bulanList = ['Januari', 'Februari', 'Maret', 'April', 'Mei', 'Juni', 'Juli', 'Agustus', 'September', 'Oktober', 'November', 'Desember'];
        const currentYear = 2026;
        let selectedYear = '2026';
        let currentRole = 'admin';

        let anggotaList = JSON.parse(localStorage.getItem('anggotaList')) || ['Suyatmo', 'Ahmad Haerul Anam', 'Muhammad Auzan', 'Budi Santoso'];
        let iuranData = JSON.parse(localStorage.getItem('iuranData')) || {
            "2026": {
                "Suyatmo": { "Januari": { nominal: 50000, bukti: '' }, "Februari": { nominal: 50000, bukti: '' } },
                "Ahmad Haerul Anam": { "Januari": { nominal: 50000, bukti: '' } }
            }
        };
        let laporanPending = JSON.parse(localStorage.getItem('laporanPending')) || [];
        let danaSosialList = JSON.parse(localStorage.getItem('danaSosialList')) || [];
        let pengeluaranList = JSON.parse(localStorage.getItem('pengeluaranList')) || [];
        let kegiatanList = JSON.parse(localStorage.getItem('kegiatanList')) || [];

        // ON LOAD INITIALIZATION
        window.onload = () => {
            updateAllData();
        };

        // --- 2. SWITCH ROLE & NAVIGASI ---
        function changeRole() {
            currentRole = document.getElementById('role-selector').value;
            updateAllData();
        }

        function switchTab(tabName) {
            ['beranda', 'iuran', 'lapor', 'kas', 'kegiatan'].forEach(t => {
                document.getElementById(`tab-${t}`).classList.add('hidden');
                const btn = document.getElementById(`btn-${t}`);
                if (btn) btn.classList.remove('active-tab');
            });

            document.getElementById(`tab-${tabName}`).classList.remove('hidden');
            const activeBtn = document.getElementById(`btn-${tabName}`);
            if (activeBtn) activeBtn.classList.add('active-tab');
        }

        function switchKasSubTab(subName) {
            ['dana-sosial', 'pengeluaran', 'laporan-kas'].forEach(s => {
                document.getElementById(`kas-sub-${s}`).classList.add('hidden');
                const btn = document.getElementById(`subnav-${s}`);
                if (btn) {
                    btn.classList.remove('bg-blue-600', 'text-white', 'font-bold');
                    btn.classList.add('text-gray-600');
                }
            });

            document.getElementById(`kas-sub-${subName}`).classList.remove('hidden');
            const activeBtn = document.getElementById(`subnav-${subName}`);
            if (activeBtn) {
                activeBtn.classList.add('bg-blue-600', 'text-white', 'font-bold');
                activeBtn.classList.remove('text-gray-600');
            }
        }

        // --- 3. LOGIKA TAMBAH DATA & LAPORAN ---
        function tambahAnggota() {
            const input = document.getElementById('input-nama-anggota-baru');
            const nama = input.value.trim();
            if (!nama) return alert('Masukkan nama anggota!');
            if (anggotaList.includes(nama)) return alert('Nama anggota sudah ada!');

            anggotaList.push(nama);
            localStorage.setItem('anggotaList', JSON.stringify(anggotaList));
            input.value = '';
            alert('Anggota berhasil ditambahkan!');
            updateAllData();
        }

        function kirimLaporan() {
            const nama = document.getElementById('input-nama').value;
            const bulan = document.getElementById('input-bulan').value;
            const nominal = parseInt(document.getElementById('input-nominal').value);
            const year = document.getElementById('filter-tahun').value;
            const fileInput = document.getElementById('input-bukti');

            if (!nominal) return alert('Nominal harus diisi!');

            const simpan = (buktiBase64 = '') => {
                const laporanBaru = {
                    id: Date.now(),
                    nama,
                    bulan,
                    tahun: year,
                    nominal,
                    bukti: buktiBase64
                };

                laporanPending.push(laporanBaru);
                localStorage.setItem('laporanPending', JSON.stringify(laporanPending));
                alert('Laporan berhasil dikirim! Menunggu konfirmasi admin.');
                fileInput.value = '';
                updateAllData();
                switchTab('beranda');
            };

            if (fileInput.files && fileInput.files[0]) {
                const reader = new FileReader();
                reader.onload = (e) => simpan(e.target.result);
                reader.readAsDataURL(fileInput.files[0]);
            } else {
                simpan();
            }
        }

        function setujuiLaporan(id) {
            const idx = laporanPending.findIndex(item => item.id === id);
            if (idx === -1) return;

            const item = laporanPending[idx];
            if (!iuranData[item.tahun]) iuranData[item.tahun] = {};
            if (!iuranData[item.tahun][item.nama]) iuranData[item.tahun][item.nama] = {};

            iuranData[item.tahun][item.nama][item.bulan] = {
                nominal: item.nominal,
                bukti: item.bukti
            };

            laporanPending.splice(idx, 1);
            localStorage.setItem('iuranData', JSON.stringify(iuranData));
            localStorage.setItem('laporanPending', JSON.stringify(laporanPending));

            alert('Pembayaran disetujui!');
            updateAllData();
        }

        function hapusLaporan(id) {
            if (!confirm('Tolak/Hapus laporan ini?')) return;
            laporanPending = laporanPending.filter(item => item.id !== id);
            localStorage.setItem('laporanPending', JSON.stringify(laporanPending));
            updateAllData();
        }

        function tambahDanaSosial() {
            const kat = document.getElementById('input-kat-dansos').value;
            const nom = parseInt(document.getElementById('input-nominal-dansos').value);
            const ket = document.getElementById('input-ket-dansos').value.trim();
            const year = document.getElementById('filter-tahun').value;

            if (!nom || !ket) return alert('Lengkapi nominal dan keterangan dana sosial!');

            const dansosBaru = {
                id: Date.now(),
                tgl: new Date().toISOString().split('T')[0],
                kategori: kat,
                nominal: nom,
                ket: ket,
                tahun: year
            };

            danaSosialList.push(dansosBaru);
            localStorage.setItem('danaSosialList', JSON.stringify(danaSosialList));

            document.getElementById('input-nominal-dansos').value = '';
            document.getElementById('input-ket-dansos').value = '';

            alert('Dana Sosial berhasil dicatat!');
            updateAllData();
        }

        function tambahPengeluaran() {
            const ket = document.getElementById('input-ket-pengeluaran').value.trim();
            const nom = parseInt(document.getElementById('input-nominal-pengeluaran').value);
            const year = document.getElementById('filter-tahun').value;
            const fileInput = document.getElementById('input-bukti-pengeluaran');

            if (!nom || !ket) return alert('Lengkapi nominal dan keterangan pengeluaran!');

            const simpanData = (buktiBase64 = '') => {
                const pengeluaranBaru = {
                    id: Date.now(),
                    tgl: new Date().toISOString().split('T')[0],
                    ket: ket,
                    nominal: nom,
                    bukti: buktiBase64,
                    tahun: year
                };

                pengeluaranList.push(pengeluaranBaru);
                localStorage.setItem('pengeluaranList', JSON.stringify(pengeluaranList));

                document.getElementById('input-ket-pengeluaran').value = '';
                document.getElementById('input-nominal-pengeluaran').value = '';
                fileInput.value = '';

                alert('Pengeluaran berhasil dicatat!');
                updateAllData();
            };

            if (fileInput.files && fileInput.files[0]) {
                const reader = new FileReader();
                reader.onload = (e) => simpanData(e.target.result);
                reader.readAsDataURL(fileInput.files[0]);
            } else {
                simpanData();
            }
        }

        function tambahKegiatan() {
            const judul = document.getElementById('input-judul-kegiatan').value.trim();
            const tgl = document.getElementById('input-tgl-kegiatan').value;
            const desc = document.getElementById('input-desc-kegiatan').value.trim();
            const fileInput = document.getElementById('input-foto-kegiatan');

            if (!judul || !tgl || !desc) return alert('Isi judul, tanggal, dan deskripsi kegiatan!');

            const simpanData = (fotoBase64 = '') => {
                const kegiatanBaru = {
                    id: Date.now(),
                    judul: judul,
                    tgl: tgl,
                    desc: desc,
                    foto: fotoBase64
                };

                kegiatanList.unshift(kegiatanBaru);
                localStorage.setItem('kegiatanList', JSON.stringify(kegiatanList));

                document.getElementById('input-judul-kegiatan').value = '';
                document.getElementById('input-tgl-kegiatan').value = '';
                document.getElementById('input-desc-kegiatan').value = '';
                fileInput.value = '';

                alert('Kegiatan berhasil ditambahkan!');
                updateAllData();
            };

            if (fileInput.files && fileInput.files[0]) {
                const reader = new FileReader();
                reader.onload = (e) => simpanData(e.target.result);
                reader.readAsDataURL(fileInput.files[0]);
            } else {
                simpanData();
            }
        }

        // --- 4. MODAL BUKTI FOTO ---
        function showBukti(src, ket = '') {
            if (!src) return alert('Tidak ada lampiran gambar');
            document.getElementById('img-bukti-target').src = src;
            document.getElementById('txt-bukti-ket').innerText = ket;
            document.getElementById('modal-bukti').classList.remove('hidden');
        }

        function closeModalBukti() {
            document.getElementById('modal-bukti').classList.add('hidden');
        }

        // --- 5. RENDER & UPDATE SELURUH DATA UI ---
        function updateAllData() {
            selectedYear = document.getElementById('filter-tahun').value || '2026';
            const isCurrentYear = parseInt(selectedYear) === currentYear;
            const curBulanIdx = new Date().getMonth();
            const curBulanName = bulanList[curBulanIdx];

            // Kontrol Tampilan Khusus Admin
            const isAdmin = currentRole === 'admin';
            document.getElementById('admin-approval-panel').classList.toggle('hidden', !isAdmin);
            document.getElementById('admin-tambah-anggota-form').classList.toggle('hidden', !isAdmin);
            document.getElementById('admin-dansos-form').classList.toggle('hidden', !isAdmin);
            document.getElementById('admin-pengeluaran-form').classList.toggle('hidden', !isAdmin);

            // Update Dropdown Nama Lapor
            const selectNama = document.getElementById('input-nama');
            selectNama.innerHTML = anggotaList.map(a => `<option value="${a}">${a}</option>`).join('');
            document.getElementById('total-anggota-count').innerText = `${anggotaList.length} Anggota`;

            // 1. Render Panel Laporan Pending (Admin)
            const listLaporanEl = document.getElementById('admin-laporan-list');
            if (laporanPending.length === 0) {
                listLaporanEl.innerHTML = '<p class="text-[11px] text-amber-700 font-medium">Tidak ada antrean laporan pembayaran.</p>';
            } else {
                listLaporanEl.innerHTML = laporanPending.map(item => `
                    <div class="bg-white p-2 rounded-xl shadow-sm border flex justify-between items-center text-xs">
                        <div>
                            <p class="font-bold text-gray-800">${item.nama} (${item.bulan} ${item.tahun})</p>
                            <p class="text-[10px] text-gray-500">Rp ${item.nominal.toLocaleString('id-ID')}</p>
                        </div>
                        <div class="flex space-x-1">
                            <button onclick="showBukti('${item.bukti}', '${item.nama} - ${item.bulan} ${item.tahun}')" class="bg-gray-100 text-gray-700 px-2 py-1 rounded font-bold text-[10px]"><i class="fas fa-eye"></i></button>
                            <button onclick="setujuiLaporan(${item.id})" class="bg-emerald-600 text-white px-2 py-1 rounded font-bold text-[10px]"><i class="fas fa-check"></i></button>
                            <button onclick="hapusLaporan(${item.id})" class="bg-rose-600 text-white px-2 py-1 rounded font-bold text-[10px]"><i class="fas fa-times"></i></button>
                        </div>
                    </div>
                `).join('');
            }

            // 2. Render Tabel Iuran Warga
            const tHeader = document.getElementById('tabel-iuran-header');
            tHeader.innerHTML = '<th class="p-2 border-b">No</th><th class="p-2 border-b sticky-col">Nama</th>';
            bulanList.forEach(b => tHeader.innerHTML += `<th class="p-2 border-b text-center">${b}</th>`);

            const tBody = document.getElementById('tabel-iuran-body');
            tBody.innerHTML = '';

            let countBayarBulanIni = 0;
            let iuranBulanIniTotal = 0;
            let totalIuranMasukSesuaiTahun = 0;

            const dataTahunIni = iuranData[selectedYear] || {};

            anggotaList.forEach((nama, idx) => {
                let rowHtml = `<tr><td class="p-2 border-b font-medium text-gray-500">${idx + 1}</td><td class="p-2 border-b font-semibold sticky-col text-gray-800">${nama}</td>`;
                const records = dataTahunIni[nama] || {};

                bulanList.forEach(bln => {
                    if (records[bln]) {
                        const nom = records[bln].nominal;
                        totalIuranMasukSesuaiTahun += nom;
                        if (bln === curBulanName && isCurrentYear) {
                            countBayarBulanIni++;
                            iuranBulanIniTotal += nom;
                        }
                        rowHtml += `<td class="p-2 border-b text-center"><button onclick="showBukti('${records[bln].bukti}', '${nama} - ${bln} ${selectedYear}')" class="bg-emerald-100 text-emerald-800 font-bold px-1.5 py-0.5 rounded text-[10px] hover:bg-emerald-200">Lunas</button></td>`;
                    } else {
                        rowHtml += `<td class="p-2 border-b text-center text-gray-300">-</td>`;
                    }
                });

                rowHtml += '</tr>';
                tBody.innerHTML += rowHtml;
            });

            // 3. Render Dana Sosial
            const filteredDansos = danaSosialList.filter(d => d.tahun === selectedYear);
            const listDansosBody = document.getElementById('list-dana-sosial-body');
            listDansosBody.innerHTML = '';
            let totalDansos = 0;

            filteredDansos.forEach(d => {
                totalDansos += d.nominal;
                listDansosBody.innerHTML += `
                    <tr>
                        <td class="p-2 border-b font-mono text-[10px] text-gray-500">${d.tgl}</td>
                        <td class="p-2 border-b font-bold text-rose-700">${d.kategori}</td>
                        <td class="p-2 border-b text-gray-700">${d.ket}</td>
                        <td class="p-2 border-b text-right font-bold text-gray-800">Rp ${d.nominal.toLocaleString('id-ID')}</td>
                    </tr>
                `;
            });
            document.getElementById('total-dana-sosial-txt').innerText = `Rp ${totalDansos.toLocaleString('id-ID')}`;

            // 4. Render Pengeluaran
            const filteredPengeluaran = pengeluaranList.filter(p => p.tahun === selectedYear);
            const listPengeluaranBody = document.getElementById('list-pengeluaran-body');
            listPengeluaranBody.innerHTML = '';
            let totalPengeluaran = 0;

            filteredPengeluaran.forEach(p => {
                totalPengeluaran += p.nominal;
                const btnBukti = p.bukti ? `<button onclick="showBukti('${p.bukti}', '${p.ket}')" class="bg-blue-100 text-blue-700 p-1 rounded font-bold text-[10px]"><i class="fas fa-image"></i></button>` : '-';
                listPengeluaranBody.innerHTML += `
                    <tr>
                        <td class="p-2 border-b font-mono text-[10px] text-gray-500">${p.tgl}</td>
                        <td class="p-2 border-b font-medium text-gray-800">${p.ket}</td>
                        <td class="p-2 border-b text-right font-bold text-gray-800">Rp ${p.nominal.toLocaleString('id-ID')}</td>
                        <td class="p-2 border-b text-center">${btnBukti}</td>
                    </tr>
                `;
            });
            document.getElementById('total-pengeluaran-txt').innerText = `Rp ${totalPengeluaran.toLocaleString('id-ID')}`;

            // 5. Render Laporan Arus Kas Bulanan
            const kasBody = document.getElementById('tabel-laporan-kas-body');
            kasBody.innerHTML = '';
            let akumulasiSaldo = 0;

            bulanList.forEach((bln, idx) => {
                let masukanBulan = 0;
                anggotaList.forEach(nama => {
                    if (dataTahunIni[nama] && dataTahunIni[nama][bln]) {
                        masukanBulan += dataTahunIni[nama][bln].nominal;
                    }
                });

                const pengeluaranBulan = filteredPengeluaran
                    .filter(p => new Date(p.tgl).getMonth() === idx)
                    .reduce((acc, curr) => acc + curr.nominal, 0);

                const dansosBulan = filteredDansos
                    .filter(d => new Date(d.tgl).getMonth() === idx)
                    .reduce((acc, curr) => acc + curr.nominal, 0);

                akumulasiSaldo += (masukanBulan - pengeluaranBulan - dansosBulan);

                kasBody.innerHTML += `
                    <tr>
                        <td class="p-1.5 border font-semibold text-gray-700">${bln}</td>
                        <td class="p-1.5 border text-right text-emerald-600 font-medium">Rp ${masukanBulan.toLocaleString('id-ID')}</td>
                        <td class="p-1.5 border text-right text-orange-600 font-medium">Rp ${pengeluaranBulan.toLocaleString('id-ID')}</td>
                        <td class="p-1.5 border text-right text-rose-600 font-medium">Rp ${dansosBulan.toLocaleString('id-ID')}</td>
                        <td class="p-1.5 border text-right font-bold text-gray-800">Rp ${akumulasiSaldo.toLocaleString('id-ID')}</td>
                    </tr>
                `;
            });

            // 6. Render Riwayat Kegiatan
            const listKegiatanEl = document.getElementById('list-riwayat-kegiatan');
            listKegiatanEl.innerHTML = kegiatanList.map(k => `
                <div class="bg-white p-3 rounded-xl shadow border space-y-2">
                    <div class="flex justify-between items-start">
                        <h3 class="font-bold text-sm text-gray-800">${k.judul}</h3>
                        <span class="text-[10px] bg-blue-50 text-blue-600 px-2 py-0.5 rounded-full font-bold">${k.tgl}</span>
                    </div>
                    <p class="text-xs text-gray-600 leading-relaxed">${k.desc}</p>
                    ${k.foto ? `<button onclick="showBukti('${k.foto}', '${k.judul}')" class="text-xs text-blue-600 font-bold flex items-center space-x-1 mt-1"><i class="fas fa-image"></i> <span>Lihat Foto Kegiatan</span></button>` : ''}
                </div>
            `).join('');

            // 7. Update Dashboard Stats Total
            const totalBeban = totalPengeluaran + totalDansos;
            const saldoKas = totalIuranMasukSesuaiTahun - totalBeban;

            document.getElementById('total-saldo-display').innerText = `Rp ${saldoKas.toLocaleString('id-ID')}`;
            document.getElementById('stat-bayar-bulan').innerHTML = `${countBayarBulanIni} / ${anggotaList.length} <span class="text-xs font-normal">anggota</span>`;
            document.getElementById('stat-iuran-bulan').innerText = `Rp ${iuranBulanIniTotal.toLocaleString('id-ID')}`;
            document.getElementById('stat-total-iuran').innerText = `Rp ${totalIuranMasukSesuaiTahun.toLocaleString('id-ID')}`;
            document.getElementById('stat-total-pengeluaran').innerText = `Rp ${totalBeban.toLocaleString('id-ID')}`;
        }
    </script>
</body>
</html>
