<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Keluarga Besar Wheel</title>
    <!-- Tailwind CSS & FontAwesome CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <style>
        body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif; }
        .active-tab { color: #2563eb; }
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
                    <option value="anggota">Anggota Keluarga</option>
                    <option value="admin">Admin / Pengurus</option>
                </select>
            </div>

            <div class="mb-4 text-left">
                <label class="block text-xs font-semibold text-gray-600 mb-1">Password</label>
                <input type="password" id="password-input" placeholder="••••••••" class="w-full px-4 py-2.5 border rounded-xl focus:ring-2 focus:ring-blue-500 text-sm">
            </div>

            <button onclick="login()" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-semibold py-3 rounded-xl transition text-sm">Masuk</button>
            <p class="text-xs text-gray-400 mt-4">Keluarga Besar Wheel © 2026</p>
        </div>
    </div>

    <!-- MODAL GANTI PASSWORD -->
    <div id="modal-password" class="fixed inset-0 bg-black/50 z-50 flex items-center justify-center p-4 hidden">
        <div class="bg-white w-full max-w-xs rounded-2xl p-5 shadow-xl">
            <h3 class="font-bold text-gray-800 text-base mb-3">Ganti Password</h3>
            <div class="space-y-3">
                <div>
                    <label class="text-xs text-gray-600 font-semibold">Password Lama</label>
                    <input type="password" id="pw-lama" class="w-full p-2 border rounded-lg text-sm">
                </div>
                <div>
                    <label class="text-xs text-gray-600 font-semibold">Password Baru</label>
                    <input type="password" id="pw-baru" class="w-full p-2 border rounded-lg text-sm">
                </div>
            </div>
            <div class="flex gap-2 mt-4">
                <button onclick="closeModalPw()" class="w-1/2 bg-gray-200 text-gray-700 py-2 rounded-lg text-xs font-semibold">Batal</button>
                <button onclick="simpanPasswordBaru()" class="w-1/2 bg-blue-600 text-white py-2 rounded-lg text-xs font-semibold">Simpan</button>
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
                <div class="flex items-center space-x-1">
                    <button onclick="openModalPw()" title="Ganti Password" class="bg-white/20 p-2 rounded-lg text-xs hover:bg-white/30"><i class="fas fa-key"></i></button>
                    <button onclick="logout()" title="Keluar" class="bg-red-500 p-2 rounded-lg text-xs hover:bg-red-600"><i class="fas fa-sign-out-alt"></i></button>
                </div>
            </div>
            <div class="flex justify-between items-center text-xs text-blue-100">
                <p>Status: <b id="user-role-label" class="capitalize">Anggota</b></p>
                <p>Iuran: Rp 50.000/bln</p>
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
                    <p class="text-xs">Bayar Juli</p>
                    <p class="text-xl font-bold mt-1" id="stat-bayar-juli">0 / 0 <span class="text-xs font-normal">kk</span></p>
                </div>
                <div class="bg-purple-600 text-white p-3 rounded-2xl">
                    <p class="text-xs">Iuran Juli</p>
                    <p class="text-xl font-bold mt-1" id="stat-iuran-juli">Rp 0</p>
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
                <p class="text-xl font-mono tracking-wider font-bold">372701000814503</p>
                <p class="text-sm text-gray-300">a.n. Bendahara Keluarga</p>
                <span class="inline-block bg-blue-600 text-xs px-2 py-0.5 rounded mt-2 font-bold">BANK BRI</span>
            </div>
        </div>

        <!-- TAB: IURAN & KELOLA ANGGOTA -->
        <div id="tab-iuran" class="p-4 hidden space-y-4">
            <div class="flex justify-between items-center">
                <h2 class="text-lg font-bold">Data Pembayaran Iuran</h2>
                <span class="text-xs bg-blue-100 text-blue-700 px-2 py-1 rounded-full font-bold" id="total-anggota-count">0 Anggota</span>
            </div>

            <!-- Form Tambah Anggota Khusus Admin -->
            <div id="admin-tambah-anggota-form" class="bg-white p-3 rounded-xl shadow border hidden space-y-2">
                <p class="text-xs font-bold text-gray-700"><i class="fas fa-user-plus mr-1"></i> Tambah Anggota Baru</p>
                <div class="flex gap-2">
                    <input type="text" id="input-nama-anggota-baru" placeholder="Nama Anggota..." class="w-full p-2 border rounded-lg text-xs">
                    <button onclick="tambahAnggota()" class="bg-blue-600 text-white px-3 py-2 rounded-lg text-xs font-bold">Tambah</button>
                </div>
            </div>

            <div class="overflow-x-auto bg-white rounded-xl shadow border">
                <table class="w-full text-left text-xs">
                    <thead class="bg-blue-600 text-white">
                        <tr>
                            <th class="p-2">No</th>
                            <th class="p-2">Nama</th>
                            <th class="p-2 text-center">Juli</th>
                            <th class="p-2 text-center">Aksi</th>
                        </tr>
                    </thead>
                    <tbody id="tabel-iuran-body" class="divide-y">
                        <!-- Dynamic Content via JS -->
                    </tbody>
                </table>
            </div>
        </div>

        <!-- TAB: LAPOR (Upload Anggota) -->
        <div id="tab-lapor" class="p-4 space-y-4 hidden">
            <h2 class="text-lg font-bold">Lapor Pembayaran</h2>
            <form onsubmit="kirimLaporan(event)" class="bg-white p-4 rounded-xl shadow space-y-3">
                <div>
                    <label class="text-xs font-semibold text-gray-600">Pilih Nama Anggota</label>
                    <select id="input-nama" required class="w-full p-2 border rounded-lg text-sm">
                        <!-- Options generated dynamically -->
                    </select>
                </div>
                <div class="grid grid-cols-2 gap-2">
                    <div>
                        <label class="text-xs font-semibold text-gray-600">Nominal (Rp)</label>
                        <input type="number" id="input-nominal" value="50000" required class="w-full p-2 border rounded-lg text-sm">
                    </div>
                    <div>
                        <label class="text-xs font-semibold text-gray-600">Bulan</label>
                        <select id="input-bulan" class="w-full p-2 border rounded-lg text-sm">
                            <option value="Juli">Juli</option>
                            <option value="Agustus">Agustus</option>
                            <option value="September">September</option>
                        </select>
                    </div>
                </div>
                <div>
                    <label class="text-xs font-semibold text-gray-600">Bukti Transfer (JPG/PNG)</label>
                    <input type="file" required class="w-full text-xs p-2 border rounded-lg">
                </div>
                <button type="submit" class="w-full bg-blue-600 text-white py-2.5 rounded-lg font-bold text-xs uppercase tracking-wide">Kirim Laporan Ke Admin</button>
            </form>
        </div>

        <!-- TAB: KAS & PENGELUARAN -->
        <div id="tab-kas" class="p-4 space-y-4 hidden">
            <h2 class="text-lg font-bold">Laporan Arus Kas</h2>
            
            <!-- Form Catat Pengeluaran Khusus Admin -->
            <div id="admin-pengeluaran-form" class="bg-white p-3 rounded-xl shadow border hidden space-y-2">
                <p class="text-xs font-bold text-red-600"><i class="fas fa-minus-circle mr-1"></i> Catat Pengeluaran Baru</p>
                <div class="grid grid-cols-2 gap-2">
                    <input type="text" id="input-ket-pengeluaran" placeholder="Keterangan..." class="p-2 border rounded-lg text-xs">
                    <input type="number" id="input-nominal-pengeluaran" placeholder="Nominal (Rp)..." class="p-2 border rounded-lg text-xs">
                </div>
                <button onclick="tambahPengeluaran()" class="w-full bg-red-600 text-white py-1.5 rounded-lg text-xs font-bold">Simpan Pengeluaran</button>
            </div>

            <div class="bg-white p-4 rounded-xl shadow text-xs space-y-2">
                <div class="flex justify-between border-b pb-2"><span>Total Iuran Masuk:</span><span class="font-bold text-green-600" id="kas-iuran-masuk">Rp 0</span></div>
                <div class="flex justify-between border-b pb-2"><span>Total Pengeluaran:</span><span class="font-bold text-red-600" id="kas-total-pengeluaran">Rp 0</span></div>
                <div class="flex justify-between pt-1 font-bold text-sm"><span>Saldo Kas Saat Ini:</span><span class="text-blue-600" id="kas-saldo-akhir">Rp 0</span></div>
            </div>

            <h3 class="text-sm font-bold text-gray-700 mt-4">Riwayat Pengeluaran</h3>
            <div id="riwayat-pengeluaran-list" class="space-y-2">
                <!-- Dynamic List -->
            </div>
        </div>

        <!-- TAB: KEGIATAN -->
        <div id="tab-kegiatan" class="p-4 hidden">
            <h2 class="text-lg font-bold mb-3">Notulensi & Kegiatan</h2>
            <div class="bg-white p-3 rounded-xl shadow text-xs">
                <p class="font-bold text-blue-600">Rapat Arisan Keluarga</p>
                <p class="text-gray-500">22 Juli 2026</p>
                <p class="mt-1">Keputusan: Pembayaran iuran disepakati rutin setiap awal bulan.</p>
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
        // Data Awal Anggota Keluarga Besar Wheel
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

        // Storage & State Management
        let anggotaList = JSON.parse(localStorage.getItem('anggotaList')) || defaultAnggota;
        let passAnggota = localStorage.getItem('passAnggota') || '12345';
        let passAdmin = localStorage.getItem('passAdmin') || '12345';
        let currentRole = '';

        let iuranData = JSON.parse(localStorage.getItem('iuranData')) || {}; 
        let laporanPending = JSON.parse(localStorage.getItem('laporanPending')) || [];
        let pengeluaranList = JSON.parse(localStorage.getItem('pengeluaranList')) || [];

        function login() {
            const role = document.getElementById('role-select').value;
            const inputPw = document.getElementById('password-input').value;

            if (role === 'anggota' && inputPw === passAnggota) {
                currentRole = 'anggota';
            } else if (role === 'admin' && inputPw === passAdmin) {
                currentRole = 'admin';
            } else {
                alert('Password salah! Password default adalah 12345');
                return;
            }

            document.getElementById('login-screen').classList.add('hidden');
            document.getElementById('app').classList.remove('hidden');
            document.getElementById('user-role-label').innerText = currentRole;
            
            updateAllData();
        }

        function logout() {
            document.getElementById('password-input').value = '';
            document.getElementById('login-screen').classList.remove('hidden');
            document.getElementById('app').classList.add('hidden');
        }

        function openModalPw() { document.getElementById('modal-password').classList.remove('hidden'); }
        function closeModalPw() { document.getElementById('modal-password').classList.add('hidden'); }

        function simpanPasswordBaru() {
            const pwLama = document.getElementById('pw-lama').value;
            const pwBaru = document.getElementById('pw-baru').value;
            const passAktif = currentRole === 'admin' ? passAdmin : passAnggota;

            if(pwLama !== passAktif) {
                alert('Password lama Anda salah!');
                return;
            }
            if(pwBaru.trim() === '') {
                alert('Masukkan password baru!');
                return;
            }

            if(currentRole === 'admin') {
                passAdmin = pwBaru;
                localStorage.setItem('passAdmin', pwBaru);
            } else {
                passAnggota = pwBaru;
                localStorage.setItem('passAnggota', pwBaru);
            }

            alert('Password berhasil diperbarui!');
            closeModalPw();
            document.getElementById('pw-lama').value = '';
            document.getElementById('pw-baru').value = '';
        }

        function tambahAnggota() {
            const namaInput = document.getElementById('input-nama-anggota-baru');
            const nama = namaInput.value.trim();
            if(!nama) return alert('Masukkan nama anggota!');

            if(anggotaList.includes(nama)) return alert('Nama anggota sudah ada!');

            anggotaList.push(nama);
            localStorage.setItem('anggotaList', JSON.stringify(anggotaList));
            namaInput.value = '';
            updateAllData();
            alert('Anggota berhasil ditambahkan!');
        }

        function hapusAnggota(nama) {
            if(confirm(`Yakin ingin menghapus ${nama} dari daftar anggota?`)) {
                anggotaList = anggotaList.filter(a => a !== nama);
                localStorage.setItem('anggotaList', JSON.stringify(anggotaList));
                updateAllData();
            }
        }

        function kirimLaporan(e) {
            e.preventDefault();
            const nama = document.getElementById('input-nama').value;
            const nominal = parseInt(document.getElementById('input-nominal').value);
            const bulan = document.getElementById('input-bulan').value;

            const laporanBaru = { id: Date.now(), nama, nominal, bulan };
            laporanPending.push(laporanBaru);
            localStorage.setItem('laporanPending', JSON.stringify(laporanPending));
            
            alert('Laporan berhasil terkirim! Menunggu konfirmasi Admin.');
            switchTab('beranda');
            updateAllData();
        }

        function setujuiLaporan(id) {
            const lap = laporanPending.find(item => item.id === id);
            if(lap) {
                if(!iuranData[lap.nama]) iuranData[lap.nama] = {};
                iuranData[lap.nama][lap.bulan] = (iuranData[lap.nama][lap.bulan] || 0) + lap.nominal;
                localStorage.setItem('iuranData', JSON.stringify(iuranData));

                laporanPending = laporanPending.filter(item => item.id !== id);
                localStorage.setItem('laporanPending', JSON.stringify(laporanPending));
                
                alert('Laporan disetujui! Data laporan dan saldo berhasil diperbarui.');
                updateAllData();
            }
        }

        function hapusLaporan(id) {
            if (confirm('Apakah Anda yakin ingin menghapus laporan ini?')) {
                laporanPending = laporanPending.filter(item => item.id !== id);
                localStorage.setItem('laporanPending', JSON.stringify(laporanPending));
                updateAllData();
            }
        }

        function tambahPengeluaran() {
            const ket = document.getElementById('input-ket-pengeluaran').value.trim();
            const nominal = parseInt(document.getElementById('input-nominal-pengeluaran').value);

            if(!ket || !nominal) return alert('Isi keterangan dan nominal pengeluaran!');

            pengeluaranList.push({ id: Date.now(), ket, nominal, tanggal: new Date().toLocaleDateString('id-ID') });
            localStorage.setItem('pengeluaranList', JSON.stringify(pengeluaranList));

            document.getElementById('input-ket-pengeluaran').value = '';
            document.getElementById('input-nominal-pengeluaran').value = '';
            updateAllData();
            alert('Pengeluaran berhasil dicatat!');
        }

        function updateAllData() {
            // Calculate Total Iuran
            let totalIuranMasuk = 0;
            let bayarJuliCount = 0;
            let totalIuranJuli = 0;

            Object.keys(iuranData).forEach(nama => {
                Object.keys(iuranData[nama]).forEach(bln => {
                    totalIuranMasuk += iuranData[nama][bln];
                    if(bln === 'Juli' && iuranData[nama][bln] > 0) {
                        bayarJuliCount++;
                        totalIuranJuli += iuranData[nama][bln];
                    }
                });
            });

            // Calculate Total Pengeluaran
            let totalPengeluaran = pengeluaranList.reduce((sum, item) => sum + item.nominal, 0);
            let saldoAkhir = totalIuranMasuk - totalPengeluaran;

            // Render Dashboard Stats
            document.getElementById('total-saldo-display').innerText = `Rp ${saldoAkhir.toLocaleString('id-ID')}`;
            document.getElementById('stat-bayar-juli').innerHTML = `${bayarJuliCount} / ${anggotaList.length} <span class="text-xs font-normal">kk</span>`;
            document.getElementById('stat-iuran-juli').innerText = `Rp ${totalIuranJuli.toLocaleString('id-ID')}`;
            document.getElementById('stat-total-iuran').innerText = `Rp ${totalIuranMasuk.toLocaleString('id-ID')}`;
            document.getElementById('stat-total-pengeluaran').innerText = `Rp ${totalPengeluaran.toLocaleString('id-ID')}`;

            // Kas Page Stats
            document.getElementById('kas-iuran-masuk').innerText = `Rp ${totalIuranMasuk.toLocaleString('id-ID')}`;
            document.getElementById('kas-total-pengeluaran').innerText = `Rp ${totalPengeluaran.toLocaleString('id-ID')}`;
            document.getElementById('kas-saldo-akhir').innerText = `Rp ${saldoAkhir.toLocaleString('id-ID')}`;

            // Render Member Dropdown
            const selectNama = document.getElementById('input-nama');
            selectNama.innerHTML = '';
            anggotaList.sort().forEach(nama => {
                selectNama.innerHTML += `<option value="${nama}">${nama}</option>`;
            });

            // Render Tabel Iuran
            const tabelBody = document.getElementById('tabel-iuran-body');
            tabelBody.innerHTML = '';
            document.getElementById('total-anggota-count').innerText = `${anggotaList.length} Anggota`;

            anggotaList.forEach((nama, idx) => {
                const lunasJuli = iuranData[nama] && iuranData[nama]['Juli'] > 0;
                const tr = document.createElement('tr');
                tr.innerHTML = `
                    <td class="p-2">${idx + 1}</td>
                    <td class="p-2 font-semibold text-gray-800">${nama}</td>
                    <td class="p-2 text-center font-bold">${lunasJuli ? '<span class="text-green-600">✓</span>' : '<span class="text-gray-300">—</span>'}</td>
                    <td class="p-2 text-center">
                        ${currentRole === 'admin' ? `<button onclick="hapusAnggota('${nama}') text-red-500 hover:text-red-700"><i class="fas fa-user-minus"></i></button>` : '-'}
                    </td>
                `;
                tabelBody.appendChild(tr);
            });

            // Admin Toggle Panels
            if(currentRole === 'admin') {
                document.getElementById('admin-approval-panel').classList.remove('hidden');
                document.getElementById('admin-tambah-anggota-form').classList.remove('hidden');
                document.getElementById('admin-pengeluaran-form').classList.remove('hidden');

                // Render Admin Approval Pending List
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
                                    <p class="text-gray-500">Bulan: ${item.bulan} - Rp ${item.nominal.toLocaleString('id-ID')}</p>
                                </div>
                                <div class="flex space-x-1">
                                    <button onclick="setujuiLaporan(${item.id})" class="bg-green-600 text-white px-2 py-1 rounded hover:bg-green-700 font-bold"><i class="fas fa-check"></i> Setujui</button>
                                    <button onclick="hapusLaporan(${item.id})" class="bg-red-500 text-white px-2 py-1 rounded hover:bg-red-600"><i class="fas fa-trash"></i></button>
                                </div>
                            </div>
                        `;
                    });
                }
            } else {
                document.getElementById('admin-approval-panel').classList.add('hidden');
                document.getElementById('admin-tambah-anggota-form').classList.add('hidden');
                document.getElementById('admin-pengeluaran-form').classList.add('hidden');
            }

            // Render Riwayat Pengeluaran List
            const expList = document.getElementById('riwayat-pengeluaran-list');
            expList.innerHTML = '';
            if(pengeluaranList.length === 0) {
                expList.innerHTML = '<p class="text-xs text-gray-400">Belum ada data pengeluaran.</p>';
            } else {
                pengeluaranList.slice().reverse().forEach(exp => {
                    expList.innerHTML += `
                        <div class="bg-white p-2.5 rounded-xl shadow-sm border text-xs flex justify-between items-center">
                            <div>
                                <p class="font-bold text-gray-800">${exp.ket}</p>
                                <p class="text-gray-400 text-[10px]">${exp.tanggal}</p>
                            </div>
                            <span class="font-bold text-red-600">- Rp ${exp.nominal.toLocaleString('id-ID')}</span>
                        </div>
                    `;
                });
            }
        }

        function switchTab(tabName) {
            const tabs = ['beranda', 'iuran', 'lapor', 'kas', 'kegiatan'];
            tabs.forEach(tab => {
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
