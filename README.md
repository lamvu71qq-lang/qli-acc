<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no, viewport-fit=cover">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <title>Quản Lý Acc Game</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        body { background-color: #0f172a; -webkit-tap-highlight-color: transparent; }
        .app-gradient { background: linear-gradient(135deg, #1e293b 0%, #0f172a 100%); }
        input:focus { outline: 2px solid #3b82f6; }
        .hidden { display: none; }
    </style>
</head>
<body class="text-slate-200 min-h-screen font-sans">

    <!-- MÀN HÌNH ĐĂNG NHẬP -->
    <div id="login-page" class="flex flex-col items-center justify-center min-h-screen p-6">
        <div class="w-full max-w-sm bg-slate-800 p-8 rounded-2xl shadow-2xl border border-slate-700">
            <h1 class="text-3xl font-extrabold text-center mb-8 text-blue-400 tracking-tight">GAME LOGIN</h1>
            <div class="space-y-4">
                <input type="text" id="user" placeholder="Tài khoản" class="w-full p-4 rounded-xl bg-slate-900 border border-slate-700 text-white">
                <input type="password" id="pass" placeholder="Mật khẩu" class="w-full p-4 rounded-xl bg-slate-900 border border-slate-700 text-white">
                <button onclick="login()" class="w-full bg-blue-600 hover:bg-blue-500 py-4 rounded-xl font-bold text-lg transition-all active:scale-95 shadow-lg">ĐĂNG NHẬP</button>
            </div>
            <p class="text-slate-500 text-center mt-6 text-sm">Hệ thống quản lý nội bộ v1.0</p>
        </div>
    </div>

    <!-- MÀN HÌNH ADMIN (gbao) -->
    <div id="admin-page" class="hidden p-4">
        <div class="max-w-4xl mx-auto">
            <div class="flex justify-between items-center mb-6 bg-slate-800 p-4 rounded-xl">
                <h2 class="text-xl font-bold text-yellow-400">ADMIN: GBAO</h2>
                <button onclick="logout()" class="bg-red-500/20 text-red-400 px-4 py-2 rounded-lg font-medium">Thoát</button>
            </div>

            <!-- Form tạo User -->
            <div class="bg-slate-800 p-6 rounded-2xl mb-6 border border-yellow-500/30">
                <h3 class="font-bold mb-4 uppercase text-sm tracking-widest text-slate-400">Tạo tài khoản User mới</h3>
                <div class="grid grid-cols-1 md:grid-cols-3 gap-3">
                    <input type="text" id="new-u" placeholder="Tên User" class="bg-slate-900 p-3 rounded-lg border border-slate-700">
                    <input type="text" id="new-p" placeholder="Mật khẩu" class="bg-slate-900 p-3 rounded-lg border border-slate-700">
                    <button onclick="addUser()" class="bg-yellow-600 hover:bg-yellow-500 p-3 rounded-lg font-bold transition">TẠO USER</button>
                </div>
            </div>

            <!-- Danh sách tất cả Acc -->
            <h3 class="font-bold mb-3 text-slate-400">TẤT CẢ TÀI KHOẢN GAME ĐÃ UP</h3>
            <div class="bg-slate-800 rounded-2xl overflow-hidden shadow-xl border border-slate-700">
                <table class="w-full text-left">
                    <thead class="bg-slate-900 text-slate-400 text-xs uppercase">
                        <tr>
                            <th class="p-4">Người up</th>
                            <th class="p-4 text-blue-400">TK Game</th>
                            <th class="p-4">MK Game</th>
                            <th class="p-4 text-center">LV</th>
                        </tr>
                    </thead>
                    <tbody id="admin-table-body">
                        <!-- Dữ liệu sẽ load vào đây -->
                    </tbody>
                </table>
            </div>
        </div>
    </div>

    <!-- MÀN HÌNH USER -->
    <div id="user-page" class="hidden p-4">
        <div class="max-w-2xl mx-auto">
            <div class="flex justify-between items-center mb-6">
                <h2 class="text-xl font-bold">Chào, <span id="user-name" class="text-blue-400"></span></h2>
                <button onclick="logout()" class="text-slate-400 underline">Đăng xuất</button>
            </div>

            <div class="bg-slate-800 p-6 rounded-2xl mb-8 shadow-lg border border-blue-500/20">
                <h3 class="font-bold mb-4">UP ACC MỚI</h3>
                <div class="space-y-3">
                    <input type="text" id="acc-tk" placeholder="Tên đăng nhập game" class="bg-slate-900 p-3 rounded-lg w-full border border-slate-700">
                    <input type="text" id="acc-mk" placeholder="Mật khẩu game" class="bg-slate-900 p-3 rounded-lg w-full border border-slate-700">
                    <input type="number" id="acc-lv" placeholder="Level hiện tại" class="bg-slate-900 p-3 rounded-lg w-full border border-slate-700">
                    <button onclick="postAcc()" class="w-full bg-blue-600 p-4 rounded-xl font-bold mt-2 shadow-lg active:scale-95 transition">GỬI ACC LÊN HỆ THỐNG</button>
                </div>
            </div>

            <h3 class="font-bold mb-3 text-slate-400 italic">Lịch sử acc bạn đã up:</h3>
            <div id="user-list" class="space-y-3">
                <!-- Acc của user sẽ hiện ở đây -->
            </div>
        </div>
    </div>

    <script>
        // KHỞI TẠO DỮ LIỆU BAN ĐẦU
        let db = JSON.parse(localStorage.getItem('game_system_db')) || { users: [], accounts: [] };
        let currentUser = null;

        function saveDB() {
            localStorage.setItem('game_system_db', JSON.stringify(db));
        }

        function login() {
            const u = document.getElementById('user').value;
            const p = document.getElementById('pass').value;

            if (u === 'gbao' && p === '031008') {
                showPage('admin-page');
                loadAdminData();
            } else {
                const user = db.users.find(x => x.u === u && x.p === p);
                if (user) {
                    currentUser = u;
                    document.getElementById('user-name').innerText = u;
                    showPage('user-page');
                    loadUserData();
                } else {
                    alert('Sai thông tin đăng nhập!');
                }
            }
        }

        function addUser() {
            const u = document.getElementById('new-u').value;
            const p = document.getElementById('new-p').value;
            if (!u || !p) return alert('Vui lòng nhập đủ!');
            if (db.users.find(x => x.u === u)) return alert('User đã tồn tại!');

            db.users.push({ u, p });
            saveDB();
            alert('Đã tạo thành công user: ' + u);
            document.getElementById('new-u').value = '';
            document.getElementById('new-p').value = '';
        }

        function postAcc() {
            const tk = document.getElementById('acc-tk').value;
            const mk = document.getElementById('acc-mk').value;
            const lv = document.getElementById('acc-lv').value;

            if (!tk || !mk || !lv) return alert('Vui lòng nhập đủ thông tin acc!');

            db.accounts.push({ uploader: currentUser, tk, mk, lv });
            saveDB();
            alert('Up acc thành công!');
            document.querySelectorAll('#user-page input').forEach(i => i.value = '');
            loadUserData();
        }

        function loadAdminData() {
            const tbody = document.getElementById('admin-table-body');
            tbody.innerHTML = db.accounts.map(acc => `
                <tr class="border-t border-slate-700 hover:bg-slate-700/50">
                    <td class="p-4 font-bold text-slate-400">${acc.uploader}</td>
                    <td class="p-4 text-blue-300 font-mono">${acc.tk}</td>
                    <td class="p-4 font-mono">${acc.mk}</td>
                    <td class="p-4 text-center font-bold text-green-400">${acc.lv}</td>
                </tr>
            `).reverse().join('');
        }

        function loadUserData() {
            const list = document.getElementById('user-list');
            const myAccs = db.accounts.filter(a => a.uploader === currentUser);
            
            if (myAccs.length === 0) {
                list.innerHTML = `<p class="text-slate-600 text-center py-10">Bạn chưa up acc nào.</p>`;
                return;
            }

            list.innerHTML = myAccs.map(acc => `
                <div class="bg-slate-800 p-4 rounded-xl border-l-4 border-blue-500 flex justify-between items-center">
                    <div>
                        <div class="text-sm text-slate-500 uppercase font-bold">Tài khoản game</div>
                        <div class="font-mono text-lg">${acc.tk}</div>
                    </div>
                    <div class="text-right">
                        <div class="text-sm text-slate-500 uppercase font-bold">Level</div>
                        <div class="text-xl font-black text-blue-400">${acc.lv}</div>
                    </div>
                </div>
            `).reverse().join('');
        }

        function showPage(id) {
            document.getElementById('login-page').classList.add('hidden');
            document.getElementById('admin-page').classList.add('hidden');
            document.getElementById('user-page').classList.add('hidden');
            document.getElementById(id).classList.remove('hidden');
        }

        function logout() {
            location.reload();
        }

        function pwaMode() {
            if (window.navigator.standalone || window.matchMedia('(display-mode: standalone)').matches) {
                // Tối ưu thêm cho giao diện App
            }
        }
        pwaMode();
    </script>
</body>
</html>
