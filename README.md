<!DOCTYPE html>
<html lang="fa" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>سیستم مدیریت ماشین‌های مجازی | VM Manager</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: system-ui, 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background: linear-gradient(135deg, #0f172a 0%, #1e293b 100%);
            min-height: 100vh;
            padding: 2rem;
        }

        .container {
            max-width: 1400px;
            margin: 0 auto;
        }

        header {
            text-align: center;
            color: white;
            margin-bottom: 2rem;
        }

        header h1 {
            font-size: 2.5rem;
            margin-bottom: 0.5rem;
        }

        header p {
            color: #94a3b8;
        }

        .stats {
            display: flex;
            gap: 1rem;
            justify-content: center;
            margin-bottom: 2rem;
            flex-wrap: wrap;
        }

        .stat-card {
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            border-radius: 1rem;
            padding: 1.5rem 2rem;
            text-align: center;
            color: white;
            min-width: 150px;
        }

        .stat-card h3 {
            font-size: 1rem;
            font-weight: normal;
            color: #cbd5e1;
            margin-bottom: 0.5rem;
        }

        .stat-card p {
            font-size: 2rem;
            font-weight: bold;
        }

        .toolbar {
            text-align: center;
            margin-bottom: 2rem;
            display: flex;
            gap: 1rem;
            justify-content: center;
            flex-wrap: wrap;
        }

        .btn {
            padding: 0.75rem 1.5rem;
            border: none;
            border-radius: 0.5rem;
            font-size: 1rem;
            cursor: pointer;
            transition: all 0.2s ease;
            font-weight: bold;
        }

        .btn-primary {
            background: #3b82f6;
            color: white;
        }

        .btn-primary:hover {
            background: #2563eb;
            transform: translateY(-2px);
        }

        .btn-success {
            background: #10b981;
            color: white;
        }

        .btn-success:hover {
            background: #059669;
        }

        .btn-danger {
            background: #ef4444;
            color: white;
        }

        .btn-danger:hover {
            background: #dc2626;
        }

        .btn-warning {
            background: #f59e0b;
            color: white;
        }

        .btn-warning:hover {
            background: #d97706;
        }

        .btn-secondary {
            background: #6b7280;
            color: white;
        }

        .btn-secondary:hover {
            background: #4b5563;
        }

        .vm-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(320px, 1fr));
            gap: 1.5rem;
        }

        .vm-card {
            background: white;
            border-radius: 1rem;
            padding: 1.5rem;
            box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1);
            transition: all 0.3s ease;
            border-right: 4px solid;
        }

        .vm-card.running {
            border-right-color: #10b981;
        }

        .vm-card.stopped {
            border-right-color: #ef4444;
        }

        .vm-card h3 {
            font-size: 1.25rem;
            margin-bottom: 0.5rem;
            display: flex;
            align-items: center;
            gap: 0.5rem;
            justify-content: space-between;
        }

        .vm-status {
            display: inline-block;
            padding: 0.25rem 0.75rem;
            border-radius: 2rem;
            font-size: 0.75rem;
            font-weight: bold;
        }

        .status-running {
            background: #d1fae5;
            color: #065f46;
        }

        .status-stopped {
            background: #fee2e2;
            color: #991b1b;
        }

        .vm-specs {
            margin: 1rem 0;
            color: #4b5563;
            font-size: 0.875rem;
        }

        .vm-specs p {
            margin: 0.25rem 0;
        }

        .vm-actions {
            display: flex;
            gap: 0.5rem;
            margin-top: 1rem;
            flex-wrap: wrap;
        }

        .btn-sm {
            padding: 0.4rem 0.8rem;
            font-size: 0.8rem;
        }

        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.5);
            justify-content: center;
            align-items: center;
            z-index: 1000;
        }

        .modal-content {
            background: white;
            border-radius: 1rem;
            padding: 2rem;
            width: 90%;
            max-width: 500px;
        }

        .modal-content h2 {
            margin-bottom: 1rem;
        }

        .modal-content input {
            width: 100%;
            padding: 0.75rem;
            margin: 0.5rem 0;
            border: 1px solid #ddd;
            border-radius: 0.5rem;
            font-size: 1rem;
        }

        .modal-buttons {
            display: flex;
            gap: 1rem;
            margin-top: 1.5rem;
        }

        @media (max-width: 768px) {
            body {
                padding: 1rem;
            }
            .vm-grid {
                grid-template-columns: 1fr;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1>🖥️ سیستم مدیریت ماشین‌های مجازی</h1>
            <p>ایجاد، شروع، توقف و حذف ماشین‌های مجازی (شبیه‌سازی شده)</p>
        </header>

        <div class="stats">
            <div class="stat-card">
                <h3>مجموع VMها</h3>
                <p id="totalVMs">0</p>
            </div>
            <div class="stat-card">
                <h3>روشن</h3>
                <p id="runningVMs">0</p>
            </div>
            <div class="stat-card">
                <h3>خاموش</h3>
                <p id="stoppedVMs">0</p>
            </div>
        </div>

        <div class="toolbar">
            <button id="addVMBtn" class="btn btn-primary">➕ ایجاد ماشین مجازی جدید</button>
            <button id="resetBtn" class="btn btn-secondary">🔄 بازنشانی به حالت اولیه</button>
        </div>

        <div id="vmList" class="vm-grid"></div>
    </div>

    <!-- مودال ایجاد VM -->
    <div id="vmModal" class="modal">
        <div class="modal-content">
            <h2>➕ ایجاد ماشین مجازی جدید</h2>
            <input type="text" id="vmName" placeholder="نام ماشین مجازی" value="Server">
            <input type="number" id="vmCpu" placeholder="تعداد هسته CPU (1-8)" value="2" min="1" max="8">
            <input type="number" id="vmRam" placeholder="RAM (GB)" value="4" min="1" max="32">
            <input type="number" id="vmDisk" placeholder="دیسک (GB)" value="50" min="10" max="500">
            <div class="modal-buttons">
                <button id="confirmCreateVM" class="btn btn-primary">ایجاد</button>
                <button id="cancelModal" class="btn btn-secondary">انصراف</button>
            </div>
        </div>
    </div>

    <script>
        // دیتابیس محلی
        let virtualMachines = [];
        let nextId = 1;

        // بارگذاری از localStorage
        function loadFromStorage() {
            const saved = localStorage.getItem('virtualMachines');
            if (saved) {
                virtualMachines = JSON.parse(saved);
                nextId = virtualMachines.length > 0 ? Math.max(...virtualMachines.map(vm => vm.id)) + 1 : 1;
            } else {
                // دیتای اولیه
                virtualMachines = [
                    { id: 1, name: 'وب سرور اصلی', cpu: 2, ram: 4, disk: 50, status: 'running', createdAt: new Date().toLocaleString() },
                    { id: 2, name: 'دیتابیس سرور', cpu: 4, ram: 8, disk: 100, status: 'stopped', createdAt: new Date().toLocaleString() },
                    { id: 3, name: 'سرور تست', cpu: 1, ram: 2, disk: 20, status: 'running', createdAt: new Date().toLocaleString() }
                ];
                nextId = 4;
            }
            updateUI();
        }

        // ذخیره در localStorage
        function saveToStorage() {
            localStorage.setItem('virtualMachines', JSON.stringify(virtualMachines));
        }

        // آپدیت آمار
        function updateStats() {
            const total = virtualMachines.length;
            const running = virtualMachines.filter(vm => vm.status === 'running').length;
            const stopped = total - running;
            
            document.getElementById('totalVMs').textContent = total;
            document.getElementById('runningVMs').textContent = running;
            document.getElementById('stoppedVMs').textContent = stopped;
        }

        // رندر لیست VMها
        function renderVMList() {
            const vmListElement = document.getElementById('vmList');
            
            if (virtualMachines.length === 0) {
                vmListElement.innerHTML = '<div style="text-align: center; color: white; grid-column: 1/-1; padding: 3rem;">✨ هیچ ماشین مجازی وجود ندارد. روی دکمه "ایجاد" کلیک کنید.</div>';
                return;
            }
            
            vmListElement.innerHTML = virtualMachines.map(vm => `
                <div class="vm-card ${vm.status}">
                    <h3>
                        <span>${vm.status === 'running' ? '🟢' : '🔴'} ${escapeHtml(vm.name)}</span>
                        <span class="vm-status ${vm.status === 'running' ? 'status-running' : 'status-stopped'}">
                            ${vm.status === 'running' ? 'در حال اجرا' : 'متوقف'}
                        </span>
                    </h3>
                    <div class="vm-specs">
                        <p>🧠 CPU: ${vm.cpu} هسته</p>
                        <p>💾 RAM: ${vm.ram} GB</p>
                        <p>💽 Disk: ${vm.disk} GB</p>
                        <p>📅 ایجاد: ${vm.createdAt || 'نامشخص'}</p>
                    </div>
                    <div class="vm-actions">
                        ${vm.status === 'running' ? 
                            `<button class="btn btn-warning btn-sm" onclick="stopVM(${vm.id})">⏸️ توقف</button>` :
                            `<button class="btn btn-success btn-sm" onclick="startVM(${vm.id})">▶️ شروع</button>`
                        }
                        <button class="btn btn-danger btn-sm" onclick="deleteVM(${vm.id})">🗑️ حذف</button>
                    </div>
                </div>
            `).join('');
            
            updateStats();
        }

        // فرار از کاراکترهای خاص
        function escapeHtml(str) {
            return str.replace(/[&<>]/g, function(m) {
                if (m === '&') return '&amp;';
                if (m === '<') return '&lt;';
                if (m === '>') return '&gt;';
                return m;
            });
        }

        // شروع VM
        function startVM(id) {
            const vm = virtualMachines.find(v => v.id === id);
            if (vm && vm.status === 'stopped') {
                vm.status = 'running';
                saveToStorage();
                updateUI();
                showToast(`${vm.name} شروع شد ✅`, 'success');
            }
        }

        // توقف VM
        function stopVM(id) {
            const vm = virtualMachines.find(v => v.id === id);
            if (vm && vm.status === 'running') {
                vm.status = 'stopped';
                saveToStorage();
                updateUI();
                showToast(`${vm.name} متوقف شد ⏹️`, 'info');
            }
        }

        // حذف VM
        function deleteVM(id) {
            if (confirm('⚠️ آیا از حذف این ماشین مجازی اطمینان دارید؟')) {
                const vm = virtualMachines.find(v => v.id === id);
                virtualMachines = virtualMachines.filter(v => v.id !== id);
                saveToStorage();
                updateUI();
                showToast(`${vm.name} حذف شد ❌`, 'error');
            }
        }

        // نمایش مودال
        function showModal() {
            document.getElementById('vmModal').style.display = 'flex';
            document.getElementById('vmName').value = `Server-${nextId}`;
            document.getElementById('vmCpu').value = '2';
            document.getElementById('vmRam').value = '4';
            document.getElementById('vmDisk').value = '50';
        }

        // مخفی کردن مودال
        function hideModal() {
            document.getElementById('vmModal').style.display = 'none';
        }

        // ایجاد VM جدید از مودال
        function createVMFromModal() {
            const name = document.getElementById('vmName').value.trim();
            if (!name) {
                showToast('لطفاً نام ماشین مجازی را وارد کنید', 'error');
                return;
            }
            
            let cpu = parseInt(document.getElementById('vmCpu').value);
            let ram = parseInt(document.getElementById('vmRam').value);
            let disk = parseInt(document.getElementById('vmDisk').value);
            
            cpu = Math.min(8, Math.max(1, cpu || 2));
            ram = Math.min(32, Math.max(1, ram || 4));
            disk = Math.min(500, Math.max(10, disk || 50));
            
            const newVM = {
                id: nextId++,
                name: name,
                cpu: cpu,
                ram: ram,
                disk: disk,
                status: 'stopped',
                createdAt: new Date().toLocaleString()
            };
            
            virtualMachines.push(newVM);
            saveToStorage();
            updateUI();
            hideModal();
            showToast(`✅ ماشین مجازی "${name}" با موفقیت ایجاد شد`, 'success');
        }

        // بازنشانی به حالت اولیه
        function resetToDefault() {
            if (confirm('⚠️ این کار تمام ماشین‌های مجازی فعلی را حذف و به حالت اولیه بازمی‌گرداند. ادامه می‌دهید؟')) {
                virtualMachines = [
                    { id: 1, name: 'وب سرور اصلی', cpu: 2, ram: 4, disk: 50, status: 'running', createdAt: new Date().toLocaleString() },
                    { id: 2, name: 'دیتابیس سرور', cpu: 4, ram: 8, disk: 100, status: 'stopped', createdAt: new Date().toLocaleString() },
                    { id: 3, name: 'سرور تست', cpu: 1, ram: 2, disk: 20, status: 'running', createdAt: new Date().toLocaleString() }
                ];
                nextId = 4;
                saveToStorage();
                updateUI();
                showToast('🔄 سیستم به حالت اولیه بازنشانی شد', 'info');
            }
        }

        // نمایش پیام موقت
        function showToast(message, type = 'info') {
            const toast = document.createElement('div');
            toast.textContent = message;
            toast.style.position = 'fixed';
            toast.style.bottom = '20px';
            toast.style.right = '20px';
            toast.style.backgroundColor = type === 'error' ? '#ef4444' : type === 'success' ? '#10b981' : '#3b82f6';
            toast.style.color = 'white';
            toast.style.padding = '12px 24px';
            toast.style.borderRadius = '8px';
            toast.style.zIndex = '1000';
            toast.style.fontSize = '14px';
            toast.style.boxShadow = '0 4px 12px rgba(0,0,0,0.15)';
            toast.style.animation = 'slideIn 0.3s ease';
            document.body.appendChild(toast);
            
            setTimeout(() => {
                toast.style.animation = 'slideOut 0.3s ease';
                setTimeout(() => toast.remove(), 300);
            }, 3000);
        }

        // اضافه کردن انیمیشن‌ها
        const style = document.createElement('style');
        style.textContent = `
            @keyframes slideIn {
                from {
                    transform: translateX(100%);
                    opacity: 0;
                }
                to {
                    transform: translateX(0);
                    opacity: 1;
                }
            }
            @keyframes slideOut {
                from {
                    transform: translateX(0);
                    opacity: 1;
                }
                to {
                    transform: translateX(100%);
                    opacity: 0;
                }
            }
        `;
        document.head.appendChild(style);

        // آپدیت کامل UI
        function updateUI() {
            renderVMList();
        }

        // رویدادها
        document.addEventListener('DOMContentLoaded', () => {
            loadFromStorage();
            document.getElementById('addVMBtn').addEventListener('click', showModal);
            document.getElementById('resetBtn').addEventListener('click', resetToDefault);
            document.getElementById('confirmCreateVM').addEventListener('click', createVMFromModal);
            document.getElementById('cancelModal').addEventListener('click', hideModal);
            
            // بستن مودال با کلیک بیرون
            window.addEventListener('click', (e) => {
                if (e.target === document.getElementById('vmModal')) {
                    hideModal();
                }
            });
        });

        // expose functions to global scope
        window.startVM = startVM;
        window.stopVM = stopVM;
        window.deleteVM = deleteVM;
    </script>
</body>
</html>
