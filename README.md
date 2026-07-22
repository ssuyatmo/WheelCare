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

        function showBukti(src, ket = '') {
            if (!src) return alert('Tidak ada lampiran gambar');
            document.getElementById('img-bukti-target').src = src;
            document.getElementById('txt-bukti-ket').innerText = ket;
            document.getElementById('modal-bukti').classList.remove('hidden');
        }

        function closeModalBukti() {
            document.getElementById('modal-bukti').classList.add('hidden');
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
                    btn.classList.remove('bg-blue-600', 'text-white');
                    btn.classList.add('text-gray-600');
                }
            });

            document.getElementById(`kas-sub-${subName}`).classList.remove('hidden');
            const activeBtn = document.getElementById(`subnav-${subName}`);
            if (activeBtn) {
                activeBtn.classList.add('bg-blue-600', 'text-white');
                activeBtn.classList.remove('text-gray-600');
            }
        }

        function updateAllData() {
            selectedYear = document.getElementById('filter-tahun').value || currentYear;
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
