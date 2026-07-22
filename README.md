<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Keluarga Besar Wheel</title>
    <!-- Tailwind CSS CDN -->
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
                <i class="fas font-bold text-4xl fa-users text-white"></i>
            </div>
            <h1 class="text-2xl font-bold">Keluarga Besar Wheel</h1>
            <p class="text-sm opacity-80">Sistem Laporan Keuangan Keluarga</p>
        </div>
        <div class="bg-white w-full max-w-sm rounded-2xl p-6 shadow-xl text-center">
            <h2 class="text-lg font-bold text-gray-800 mb-4">Masuk ke Aplikasi</h2>
            <div class="mb-4 text-left">
                <label class="block text-sm font-medium text-gray-700 mb-1">Password</label>
                <input type="password" id="password-input" placeholder="Masukkan password..." class="w-full px-4 py-3 border border-gray-300 rounded-xl focus:ring-2 focus:ring-blue-500 focus:outline-none">
            </div>
            <button onclick="login()" class="w-full bg-blue-600 hover:bg-blue-700 text-white font-semibold py-3 rounded-xl transition">Masuk</button>
            <p class="text-xs text-gray-400 mt-4">Keluarga Besar Wheel</p>
        </div>
    </div>

    <!-- MAIN APP CONTENT -->
    <div id="app" class="max-w-md mx-auto bg-gray-50 min-h-screen shadow-md relative hidden">
        
        <!-- HEADER -->
        <div class="bg-blue-600 text-white p-4 rounded-b-2xl shadow-lg">
            <div class="flex justify-between items-center mb-2">
                <div class="flex items-center space-x-2">
                    <i class="fas fa-users text-2xl"></i>
                    <h1 class="text-xl font-bold">Keluarga Besar Wheel</h1>
                </div>
                <button onclick="logout()" class="bg-red-500 p-2 rounded-lg text-xs"><i class="fas fa-sign-out-alt"></i></button>
            </div>
            <p class="text-xs text-blue-100">Masuk sebagai <b>Anggota</b> • Iuran: Rp 50.000/bulan</p>
        </div>

        <!-- TAB CONTENT: BERANDA -->
        <div id="tab-beranda" class="p-4 space-y-4">
            <!-- Saldo Utama -->
            <div class="bg-emerald-600 text-white p-4 rounded-2xl flex justify-between items-center shadow">
                <div>
                    <p class="text-xs opacity-90">Saldo Kas Saat Ini</p>
                    <p class="text-2xl font-bold">Rp 4.363.000</p>
                </div>
                <div class="w-10 h-10 bg-yellow-400 text-yellow-900 rounded-full flex items-center justify-center font-bold text-xl">$</div>
            </div>

            <!-- Grid Ringkasan -->
            <div class="grid grid-cols-2 gap-3">
                <div class="bg-blue-600 text-white p-3 rounded-2xl">
                    <p class="text-xs">Bayar Juli</p>
                    <p class="text-xl font-bold mt-1">28 / 87 <span class="text-sm font-normal">kk</span></p>
                </div>
                <div class="bg-purple-600 text-white p-3 rounded-2xl">
                    <p class="text-xs">Iuran Juli</p>
                    <p class="text-xl font-bold mt-1">Rp 3.500.000</p>
                </div>
                <div class="bg-sky-500 text-white p-3 rounded-2xl">
                    <p class="text-xs">Total Iuran Masuk</p>
                    <p class="text-xl font-bold mt-1">Rp 28.000.000</p>
                </div>
                <div class="bg-orange-500 text-white p-3 rounded-2xl">
                    <p class="text-xs">Total Pengeluaran</p>
                    <p class="text-xl font-bold mt-1">Rp 26.029.000</p>
                </div>
                <div class="bg-pink-600 text-white p-3 rounded-2xl">
                    <p class="text-xs">Total Dana Sosial</p>
                    <p class="text-xl font-bold mt-1">Rp 2.700.000</p>
                </div>
                <div class="bg-slate-700 text-white p-3 rounded-2xl">
                    <p class="text-xs">Saldo Awal (carry over)</p>
                    <p class="text-xl font-bold mt-1">Rp 5.092.000</p>
                </div>
            </div>

            <!-- Card Rekening -->
            <div class="bg-slate-800 text-white p-4 rounded-2xl shadow">
                <p class="text-xs text-gray-300 font-semibold mb-1"><i class="fas fa-university mr-1"></i> REKENING PEMBAYARAN</p>
                <p class="text-xl font-mono tracking-wider font-bold">372701000814503</p>
                <p class="text-sm text-gray-300">a.n. Bendahara Keluarga</p>
                <span class="inline-block bg-blue-600 text-xs px-2 py-0.5 rounded mt-2 font-bold">BANK BRI</span>
            </div>
        </div>

        <!-- TAB CONTENT: IURAN -->
        <div id="tab-iuran" class="p-4 hidden">
            <h2 class="text-lg font-bold mb-3">Data Pembayaran Iuran</h2>
            <div class="overflow-x-auto bg-white rounded-xl shadow border">
                <table class="w-full text-left text-xs">
                    <thead class="bg-blue-600 text-white">
                        <tr>
                            <th class="p-2">No</th>
                            <th class="p-2">Nama</th>
                            <th class="p-2">Jan</th>
                            <th class="p-2">Feb</th>
                        </tr>
                    </thead>
                    <tbody class="divide-y">
                        <tr><td class="p-2">1</td><td class="p-2 font-semibold">Budi Wheel</td><td class="p-2 text-green-600">✓</td><td class="p-2 text-green-600">✓</td></tr>
                        <tr><td class="p-2">2</td><td class="p-2 font-semibold">Andi Wheel</td><td class="p-2 text-red-500">—</td><td class="p-2 text-green-600">✓</td></tr>
                        <tr><td class="p-2">3</td><td class="p-2 font-semibold">Eka Wheel</td><td class="p-2 text-green-600">✓</td><td class="p-2 text-red-500">—</td></tr>
                    </tbody>
                </table>
            </div>
        </div>

        <!-- TAB CONTENT: LAPOR -->
        <div id="tab-lapor" class="p-4 space-y-4 hidden">
            <h2 class="text-lg font-bold">Lapor Pembayaran</h2>
            <form onsubmit="event.preventDefault(); alert('Laporan berhasil dikirim!');" class="bg-white p-4 rounded-xl shadow space-y-3">
                <div>
                    <label class="text-xs font-semibold text-gray-600">Nama Anggota</label>
                    <select class="w-full p-2 border rounded-lg text-sm">
                        <option>-- Pilih Nama --</option>
                        <option>Budi Wheel</option>
                        <option>Andi Wheel</option>
                    </select>
                </div>
                <div class="grid grid-cols-2 gap-2">
                    <div>
                        <label class="text-xs font-semibold text-gray-600">Tahun</label>
                        <select class="w-full p-2 border rounded-lg text-sm"><option>2026</option></select>
                    </div>
                    <div>
                        <label class="text-xs font-semibold text-gray-600">Bulan</label>
                        <select class="w-full p-2 border rounded-lg text-sm"><option>Juli</option></select>
                    </div>
                </div>
                <div>
                    <label class="text-xs font-semibold text-gray-600">Bukti Transfer</label>
                    <input type="file" class="w-full text-xs p-2 border rounded-lg">
                </div>
                <button type="submit" class="w-full bg-blue-600 text-white py-2 rounded-lg font-bold">Kirim Laporan</button>
            </form>
        </div>

        <!-- TAB CONTENT: KAS -->
        <div id="tab-kas" class="p-4 hidden">
            <h2 class="text-lg font-bold mb-3">Laporan Arus Kas</h2>
            <div class="bg-white p-4 rounded-xl shadow text-xs space-y-2">
                <div class="flex justify-between border-b pb-2"><span>Pemasukan Juli:</span><span class="font-bold text-green-600">Rp 3.500.000</span></div>
                <div class="flex justify-between border-b pb-2"><span>Pengeluaran Juli:</span><span class="font-bold text-red-600">Rp 1.200.000</span></div>
                <div class="flex justify-between pt-1 font-bold text-sm"><span>Saldo Akhir:</span><span class="text-blue-600">Rp 4.363.000</span></div>
            </div>
        </div>

        <!-- TAB CONTENT: KEGIATAN -->
        <div id="tab-kegiatan" class="p-4 hidden">
            <h2 class="text-lg font-bold mb-3">Notulensi & Kegiatan</h2>
            <div class="bg-white p-3 rounded-xl shadow mb-2 text-xs">
                <p class="font-bold text-blue-600">Rapat Arisan Keluarga</p>
                <p class="text-gray-500">15 Juli 2026</p>
                <p class="mt-1">Keputusan: Acara liburan bersama dilaksanakan bulan Desember.</p>
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
        function login() {
            const pw = document.getElementById('password-input').value;
            if(pw !== '') {
                document.getElementById('login-screen').classList.add('hidden');
                document.getElementById('app').classList.remove('hidden');
            } else {
                alert('Silahkan masukkan password!');
            }
        }

        function logout() {
            document.getElementById('login-screen').classList.remove('hidden');
            document.getElementById('app').classList.add('hidden');
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
