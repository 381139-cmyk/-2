[code_artifact.html](https://github.com/user-attachments/files/34001325/code_artifact.html)
<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>高中班級午餐訂餐系統</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome 圖標 -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Google Fonts: Noto Sans TC -->
    <link href="https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@400;500;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Noto Sans TC', sans-serif;
            background-color: #f3f4f6;
        }
        /* 列印樣式優化 */
        @media print {
            .no-print {
                display: none !important;
            }
            .print-only {
                display: block !important;
            }
            body {
                background-color: white;
                color: black;
            }
            .shadow-lg, .shadow-md, .shadow-sm {
                box-shadow: none !important;
            }
            .border-custom {
                border: 1px solid #ccc !important;
            }
        }
    </style>
</head>
<body class="text-gray-800 min-h-screen flex flex-col">

    <!-- 頂部導覽列 -->
    <header class="bg-indigo-600 text-white shadow-md no-print">
        <div class="max-w-7xl mx-auto px-4 py-4 flex justify-between items-center">
            <div class="flex items-center space-x-3">
                <i class="fa-solid fa-utensils text-2xl text-yellow-300"></i>
                <div>
                    <h1 class="text-xl font-bold leading-tight">高中班級午餐訂餐系統</h1>
                    <p class="text-xs text-indigo-200">每日午餐登記與統計平台</p>
                </div>
            </div>
            <div class="text-right text-sm">
                <span id="currentDate" class="bg-indigo-700 px-3 py-1 rounded-full text-indigo-100"></span>
            </div>
        </div>
    </header>

    <!-- 主內容區 -->
    <main class="max-w-7xl mx-auto px-4 py-6 flex-grow w-full grid grid-cols-1 lg:grid-cols-12 gap-6">

        <!-- 左半部：菜單顯示與訂餐登記區 (8 欄寬) -->
        <div class="lg:col-span-7 space-y-6 no-print">

            <!-- 1. 菜單展示區 -->
            <section class="bg-white rounded-xl p-5 shadow-sm border border-gray-100">
                <div class="flex justify-between items-center mb-4">
                    <h2 class="text-lg font-bold text-gray-800 flex items-center gap-2">
                        <i class="fa-solid fa-book-open text-indigo-500"></i> 今日菜單
                    </h2>
                    <span class="text-xs bg-indigo-50 text-indigo-600 px-2.5 py-1 rounded-full border border-indigo-100">共 5 項餐點</span>
                </div>
                
                <div class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-5 gap-3" id="menuCardsContainer">
                    <!-- 菜單動態載入 -->
                </div>
            </section>

            <!-- 2. 訂餐登記介面 (小老師操作) -->
            <section class="bg-white rounded-xl p-5 shadow-sm border border-gray-100">
                <h2 class="text-lg font-bold text-gray-800 mb-3 flex items-center gap-2">
                    <i class="fa-solid fa-user-pen text-indigo-500"></i> 快速訂餐登記
                </h2>
                <p class="text-xs text-gray-500 mb-4">輸入座號 (1-50) 與餐點編號 (1-5)，按下 Enter 或點擊「加入訂單」即可快速連續登記。</p>
                
                <form id="orderForm" onsubmit="handleOrderSubmit(event)" class="space-y-4">
                    <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
                        <!-- 座號輸入 -->
                        <div>
                            <label for="seatNumber" class="block text-sm font-medium text-gray-700 mb-1">學生座號 (1~50)</label>
                            <div class="relative">
                                <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none text-gray-400">
                                    <i class="fa-solid fa-hashtag"></i>
                                </div>
                                <input type="number" id="seatNumber" min="1" max="50" placeholder="例如: 12" required
                                    class="w-full pl-9 pr-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 outline-none transition-all">
                            </div>
                        </div>

                        <!-- 餐點編號選擇/輸入 -->
                        <div>
                            <label for="itemId" class="block text-sm font-medium text-gray-700 mb-1">餐點編號 (1~5)</label>
                            <div class="relative">
                                <div class="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none text-gray-400">
                                    <i class="fa-solid fa-bowl-food"></i>
                                </div>
                                <input type="number" id="itemId" min="1" max="5" placeholder="例如: 1" required
                                    class="w-full pl-9 pr-3 py-2 border border-gray-300 rounded-lg focus:ring-2 focus:ring-indigo-500 focus:border-indigo-500 outline-none transition-all">
                            </div>
                        </div>
                    </div>

                    <!-- 訊息提示區域 -->
                    <div id="alertMessage" class="hidden p-3 rounded-lg text-sm font-medium transition-all"></div>

                    <!-- 按鈕組 -->
                    <div class="flex gap-3">
                        <button type="submit" class="flex-1 bg-indigo-600 hover:bg-indigo-700 text-white font-medium py-2.5 px-4 rounded-lg shadow transition-colors flex items-center justify-center gap-2">
                            <i class="fa-solid fa-plus"></i> 新增訂單 (Enter)
                        </button>
                    </div>
                </form>
            </section>

            <!-- 3. 已登記明細對照表 -->
            <section class="bg-white rounded-xl p-5 shadow-sm border border-gray-100">
                <div class="flex justify-between items-center mb-3">
                    <h2 class="text-lg font-bold text-gray-800 flex items-center gap-2">
                        <i class="fa-solid fa-list-check text-indigo-500"></i> 已登記明細表
                    </h2>
                    <button onclick="confirmClearAll()" class="text-xs text-red-600 hover:text-red-800 hover:bg-red-50 px-2.5 py-1 rounded transition-colors border border-red-200">
                        <i class="fa-solid fa-trash-can"></i> 清空全部
                    </button>
                </div>

                <div class="overflow-y-auto max-h-64 border border-gray-200 rounded-lg">
                    <table class="w-full text-sm text-left text-gray-600">
                        <thead class="text-xs text-gray-700 uppercase bg-gray-100 sticky top-0">
                            <tr>
                                <th class="px-4 py-2">座號</th>
                                <th class="px-4 py-2">品項名稱</th>
                                <th class="px-4 py-2">單價</th>
                                <th class="px-4 py-2 text-center">操作</th>
                            </tr>
                        </thead>
                        <tbody id="orderListTable">
                            <!-- 訂單列動態產生 -->
                        </tbody>
                    </table>
                </div>
            </section>
        </div>

        <!-- 右半部：結算報表與統計 (5 欄寬 / 列印時全寬) -->
        <div class="lg:col-span-5 space-y-6 w-full">
            <section class="bg-white rounded-xl p-5 shadow-sm border border-gray-100 print:border-none print:shadow-none">
                
                <!-- 列印專用抬頭 -->
                <div class="hidden print-only mb-6 text-center">
                    <h1 class="text-2xl font-bold">高中班級午餐訂餐結算報表</h1>
                    <p class="text-sm text-gray-600" id="printDate"></p>
                </div>

                <div class="flex justify-between items-center mb-4 no-print">
                    <h2 class="text-lg font-bold text-gray-800 flex items-center gap-2">
                        <i class="fa-solid fa-calculator text-indigo-500"></i> 結算統計報表
                    </h2>
                    <button onclick="window.print()" class="bg-emerald-600 hover:bg-emerald-700 text-white text-xs px-3 py-1.5 rounded-lg flex items-center gap-1.5 transition-colors">
                        <i class="fa-solid fa-print"></i> 列印 / 匯出
                    </button>
                </div>

                <!-- 統計卡片 -->
                <div class="grid grid-cols-2 gap-3 mb-4">
                    <div class="bg-indigo-50 border border-indigo-100 rounded-lg p-3 text-center">
                        <span class="text-xs text-indigo-600 font-medium">總訂餐筆數</span>
                        <div class="text-2xl font-bold text-indigo-900 mt-0.5"><span id="totalCount">0</span> 份</div>
                    </div>
                    <div class="bg-emerald-50 border border-emerald-100 rounded-lg p-3 text-center">
                        <span class="text-xs text-emerald-600 font-medium">全班總金額</span>
                        <div class="text-2xl font-bold text-emerald-900 mt-0.5">$<span id="totalAmount">0</span></div>
                    </div>
                </div>

                <!-- 各品項統計表格 -->
                <div class="border border-gray-200 rounded-lg overflow-hidden mb-4">
                    <table class="w-full text-sm text-left text-gray-600">
                        <thead class="text-xs text-gray-700 uppercase bg-gray-50 border-b">
                            <tr>
                                <th class="px-3 py-2">餐點名稱</th>
                                <th class="px-3 py-2 text-right">單價</th>
                                <th class="px-3 py-2 text-right">數量</th>
                                <th class="px-3 py-2 text-right">小計</th>
                            </tr>
                        </thead>
                        <tbody id="summaryTableBody" class="divide-y divide-gray-200">
                            <!-- 統計內容 -->
                        </tbody>
                    </table>
                </div>

                <!-- 列印時顯示明細對照 -->
                <div class="hidden print-only mt-6">
                    <h3 class="font-bold border-b pb-1 mb-2">訂餐座號明細清單</h3>
                    <div id="printSeatDetails" class="text-xs grid grid-cols-4 gap-2">
                        <!-- 供列印時核對座號用 -->
                    </div>
                </div>

                <!-- 備註說明 (簡化資訊) -->
                <div class="text-xs text-gray-400 mt-3 pt-3 border-t border-gray-100 flex justify-between items-center no-print">
                    <span>系統自動即時更新數據</span>
                    <span>班級訂餐統計</span>
                </div>
            </section>
        </div>
    </main>

    <!-- 腳頁 -->
    <footer class="bg-white border-t border-gray-200 py-3 text-center text-xs text-gray-500 no-print">
        班級午餐訂餐系統 &copy; 高中學務處/膳食委員會 專用系統
    </footer>

    <!-- JavaScript 邏輯 -->
    <script>
        // 1. 菜單資料
        const MENU = [
            { id: 1, name: "排骨便當", price: 80, icon: "fa-drumstick-bite" },
            { id: 2, name: "雞腿便當", price: 85, icon: "fa-bowl-rice" },
            { id: 3, name: "素食便當", price: 70, icon: "fa-leaf" },
            { id: 4, name: "滷肉飯", price: 55, icon: "fa-utensils" },
            { id: 5, name: "貢丸湯", price: 20, icon: "fa-mug-hot" }
        ];

        // 2. 訂單狀態 (可儲存於 LocalStorage 保持不丟失)
        let orders = JSON.parse(localStorage.getItem('lunch_orders')) || [];

        // 頁面初始化
        window.onload = function() {
            // 顯示今日日期
            const now = new Date();
            const dateStr = `${now.getFullYear()} 年 ${now.getMonth() + 1} 月 ${now.getDate()} 日`;
            document.getElementById('currentDate').innerText = dateStr;
            document.getElementById('printDate').innerText = `列印時間：${dateStr}`;

            renderMenuCards();
            updateUI();
        };

        // 渲染菜單卡片
        function renderMenuCards() {
            const container = document.getElementById('menuCardsContainer');
            container.innerHTML = MENU.map(item => `
                <div class="border border-gray-200 rounded-lg p-3 text-center hover:border-indigo-400 transition-all bg-gray-50/50 cursor-pointer" onclick="quickSelectMenu(${item.id})">
                    <div class="w-8 h-8 mx-auto bg-indigo-100 text-indigo-600 rounded-full flex items-center justify-center mb-1 text-xs">
                        <i class="fa-solid ${item.icon}"></i>
                    </div>
                    <div class="font-bold text-gray-800 text-sm">${item.id}. ${item.name}</div>
                    <div class="text-indigo-600 font-semibold text-xs mt-0.5">$${item.price}</div>
                </div>
            `).join('');
        }

        // 點擊菜單卡片自動填入餐點編號
        function quickSelectMenu(id) {
            document.getElementById('itemId').value = id;
            document.getElementById('seatNumber').focus();
        }

        // 處理訂單提交
        function handleOrderSubmit(event) {
            event.preventDefault();
            
            const seatInput = document.getElementById('seatNumber');
            const itemInput = document.getElementById('itemId');

            const seat = parseInt(seatInput.value, 10);
            const itemId = parseInt(itemInput.value, 10);

            // 驗證邏輯
            if (isNaN(seat) || seat < 1 || seat > 50) {
                showAlert("請輸入有效的座號 (1 ~ 50)", "error");
                seatInput.focus();
                return;
            }

            if (isNaN(itemId) || itemId < 1 || itemId > 5) {
                showAlert("請輸入有效的餐點編號 (1 ~ 5)", "error");
                itemInput.focus();
                return;
            }

            // 尋找菜單項目
            const menuItem = MENU.find(m => m.id === itemId);

            // 檢查座號是否重複登記 (若已登記則覆蓋或提示)
            const existingIndex = orders.findIndex(o => o.seat === seat);
            if (existingIndex !== -1) {
                orders[existingIndex] = { seat, menuItem };
                showAlert(`座號 ${seat} 號已更新為 ${menuItem.name}`, "success");
            } else {
                orders.push({ seat, menuItem });
                showAlert(`成功新增：${seat} 號 - ${menuItem.name}`, "success");
            }

            saveAndRefresh();

            // 清空輸入框並聚焦於座號以利連續輸入
            seatInput.value = '';
            itemInput.value = '';
            seatInput.focus();
        }

        // 刪除單筆訂單
        function deleteOrder(seat) {
            orders = orders.filter(o => o.seat !== seat);
            saveAndRefresh();
            showAlert(`已刪除座號 ${seat} 的訂單`, "info");
        }

        // 清空全部訂單
        function confirmClearAll() {
            if (orders.length === 0) return;
            if (confirm("確定要清空全班的所有訂餐紀錄嗎？")) {
                orders = [];
                saveAndRefresh();
                showAlert("已清空所有訂餐紀錄", "info");
            }
        }

        // 保存並更新畫面
        function saveAndRefresh() {
            // 按照座號排序
            orders.sort((a, b) => a.seat - b.seat);
            localStorage.setItem('lunch_orders', JSON.stringify(orders));
            updateUI();
        }

        // 更新 UI 顯示
        function updateUI() {
            renderOrderListTable();
            renderSummary();
        }

        // 渲染登記明細表格
        function renderOrderListTable() {
            const tbody = document.getElementById('orderListTable');
            if (orders.length === 0) {
                tbody.innerHTML = `<tr><td colspan="4" class="px-4 py-6 text-center text-gray-400">目前尚無登記訂單</td></tr>`;
                return;
            }

            tbody.innerHTML = orders.map(order => `
                <tr class="border-b hover:bg-gray-50 transition-colors">
                    <td class="px-4 py-2 font-medium text-gray-900">${order.seat} 號</td>
                    <td class="px-4 py-2">${order.menuItem.name}</td>
                    <td class="px-4 py-2 text-indigo-600 font-medium">$${order.menuItem.price}</td>
                    <td class="px-4 py-2 text-center">
                        <button onclick="deleteOrder(${order.seat})" class="text-red-500 hover:text-red-700 p-1">
                            <i class="fa-solid fa-xmark"></i>
                        </button>
                    </td>
                </tr>
            `).join('');
        }

        // 渲染結算與報表
        function renderSummary() {
            const summaryTableBody = document.getElementById('summaryTableBody');
            const printSeatDetails = document.getElementById('printSeatDetails');

            let totalCount = orders.length;
            let totalAmount = 0;

            // 各品項數量與小計統計
            const stats = MENU.map(item => {
                const itemOrders = orders.filter(o => o.menuItem.id === item.id);
                const count = itemOrders.length;
                const subtotal = count * item.price;
                totalAmount += subtotal;
                return {
                    ...item,
                    count,
                    subtotal,
                    seats: itemOrders.map(o => o.seat)
                };
            });

            // 更新總計區塊
            document.getElementById('totalCount').innerText = totalCount;
            document.getElementById('totalAmount').innerText = totalAmount;

            // 渲染統計表
            summaryTableBody.innerHTML = stats.map(s => `
                <tr class="${s.count > 0 ? 'bg-white font-medium' : 'bg-gray-50/50 text-gray-400'}">
                    <td class="px-3 py-2.5">${s.name}</td>
                    <td class="px-3 py-2.5 text-right">$${s.price}</td>
                    <td class="px-3 py-2.5 text-right ${s.count > 0 ? 'text-indigo-600 font-bold' : ''}">${s.count}</td>
                    <td class="px-3 py-2.5 text-right">${s.subtotal > 0 ? '$' + s.subtotal : '$0'}</td>
                </tr>
            `).join('');

            // 渲染列印用對照表
            if (printSeatDetails) {
                printSeatDetails.innerHTML = orders.map(o => `
                    <div class="border p-1 rounded">
                        <strong>${o.seat}號:</strong> ${o.menuItem.name} ($${o.menuItem.price})
                    </div>
                `).join('');
            }
        }

        // 提示訊息顯示
        let alertTimeout;
        function showAlert(msg, type = "info") {
            const alertBox = document.getElementById('alertMessage');
            alertBox.classList.remove('hidden', 'bg-red-100', 'text-red-700', 'bg-emerald-100', 'text-emerald-700', 'bg-blue-100', 'text-blue-700');

            if (type === "error") {
                alertBox.classList.add('bg-red-100', 'text-red-700');
            } else if (type === "success") {
                alertBox.classList.add('bg-emerald-100', 'text-emerald-700');
            } else {
                alertBox.classList.add('bg-blue-100', 'text-blue-700');
            }

            alertBox.innerText = msg;

            clearTimeout(alertTimeout);
            alertTimeout = setTimeout(() => {
                alertBox.classList.add('hidden');
            }, 3000);
        }
    </script>
</body>
</html>
