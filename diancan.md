<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>餐厅点餐管理系统</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Microsoft YaHei', sans-serif;
        }
        
        body {
            background-color: #f5f5f5;
            color: #333;
        }
        
        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 20px;
        }
        
        header {
            background-color: #2c3e50;
            color: white;
            padding: 15px 0;
            text-align: center;
            margin-bottom: 20px;
            border-radius: 5px;
        }
        
        .section {
            background-color: white;
            border-radius: 5px;
            padding: 20px;
            margin-bottom: 20px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.1);
        }
        
        h1, h2, h3 {
            margin-bottom: 15px;
            color: #2c3e50;
        }
        
        .btn {
            padding: 10px 15px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 16px;
            transition: background-color 0.3s;
        }
        
        .btn-primary {
            background-color: #3498db;
            color: white;
        }
        
        .btn-success {
            background-color: #2ecc71;
            color: white;
        }
        
        .btn-danger {
            background-color: #e74c3c;
            color: white;
        }
        
        .btn:hover {
            opacity: 0.9;
        }
        
        table {
            width: 100%;
            border-collapse: collapse;
            margin-bottom: 15px;
        }
        
        table, th, td {
            border: 1px solid #ddd;
        }
        
        th, td {
            padding: 12px;
            text-align: left;
        }
        
        th {
            background-color: #f2f2f2;
        }
        
        tr:nth-child(even) {
            background-color: #f9f9f9;
        }
        
        .form-group {
            margin-bottom: 15px;
        }
        
        label {
            display: block;
            margin-bottom: 5px;
            font-weight: bold;
        }
        
        input, select {
            width: 100%;
            padding: 10px;
            border: 1px solid #ddd;
            border-radius: 4px;
            font-size: 16px;
        }
        
        .hidden {
            display: none;
        }
        
        .payment-option {
            display: inline-block;
            width: 23%;
            margin: 1%;
            padding: 15px;
            text-align: center;
            border: 1px solid #ddd;
            border-radius: 5px;
            cursor: pointer;
            transition: all 0.3s;
        }
        
        .payment-option:hover {
            background-color: #f0f0f0;
        }
        
        .payment-option.selected {
            border-color: #3498db;
            background-color: #e3f2fd;
        }
        
        .status-paid {
            color: #2ecc71;
            font-weight: bold;
        }
        
        .status-unpaid {
            color: #e74c3c;
            font-weight: bold;
        }
        
        .menu-item {
            display: inline-block;
            width: 23%;
            margin: 1%;
            padding: 15px;
            text-align: center;
            border: 1px solid #ddd;
            border-radius: 5px;
            cursor: pointer;
            transition: all 0.3s;
        }
        
        .menu-item:hover {
            background-color: #f0f0f0;
        }
        
        .preparation-area {
            background-color: #f9f9f9;
            padding: 15px;
            border-radius: 5px;
            margin-top: 20px;
        }
        
        .order-item {
            padding: 10px;
            margin-bottom: 10px;
            background-color: white;
            border-radius: 5px;
            border-left: 4px solid #3498db;
        }
        
        .notification {
            position: fixed;
            top: 20px;
            right: 20px;
            padding: 15px;
            background-color: #2ecc71;
            color: white;
            border-radius: 5px;
            box-shadow: 0 2px 5px rgba(0,0,0,0.2);
            z-index: 1000;
        }
        
        .tabs {
            display: flex;
            margin-bottom: 20px;
        }
        
        .tab {
            padding: 10px 20px;
            background-color: #f2f2f2;
            border: 1px solid #ddd;
            border-bottom: none;
            cursor: pointer;
            border-radius: 5px 5px 0 0;
            margin-right: 5px;
        }
        
        .tab.active {
            background-color: white;
            border-bottom: 1px solid white;
            margin-bottom: -1px;
        }
        
        .tab-content {
            border: 1px solid #ddd;
            padding: 20px;
            border-radius: 0 5px 5px 5px;
        }
        
        .receipt {
            background-color: white;
            padding: 20px;
            border: 1px solid #ddd;
            border-radius: 5px;
            font-family: monospace;
            max-width: 300px;
            margin: 0 auto;
        }
        
        .error-message {
            color: #e74c3c;
            font-size: 14px;
            margin-top: 5px;
        }
        
        .modal {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0,0,0,0.5);
            z-index: 1000;
            justify-content: center;
            align-items: center;
        }
        
        .modal-content {
            background-color: white;
            padding: 20px;
            border-radius: 5px;
            width: 400px;
            max-width: 90%;
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1>餐厅点餐管理系统</h1>
        </header>
        
        <!-- 前台顾客界面 -->
        <div id="frontend" class="section">
            <h2>顾客点餐界面</h2>
            
            <!-- 会员验证区域 -->
            <div id="member-auth">
                <h3>会员验证</h3>
                <div class="form-group">
                    <label for="member-id">会员号:</label>
                    <input type="text" id="member-id" placeholder="请输入会员号">
                    <div id="member-id-error" class="error-message hidden">请输入会员号</div>
                </div>
                <div class="form-group">
                    <label for="table-number">桌号:</label>
                    <input type="text" id="table-number" placeholder="请输入桌号">
                    <div id="table-number-error" class="error-message hidden">请输入桌号</div>
                </div>
                <button id="enter-frontend" class="btn btn-primary">进入点餐</button>
                <button id="register-member" class="btn btn-success">注册会员</button>
            </div>
            
            <!-- 点餐区域 -->
            <div id="ordering-area" class="hidden">
                <h3>菜单</h3>
                <div id="menu-items">
                    <!-- 菜品将通过JavaScript动态添加 -->
                </div>
                
                <h3>已选菜品</h3>
                <table id="selected-items">
                    <thead>
                        <tr>
                            <th>菜品名称</th>
                            <th>价格</th>
                            <th>数量</th>
                            <th>操作</th>
                        </tr>
                    </thead>
                    <tbody>
                        <!-- 已选菜品将通过JavaScript动态添加 -->
                    </tbody>
                </table>
                
                <div class="form-group">
                    <h3>合计: <span id="total-price">0</span>元</h3>
                    <button id="submit-order" class="btn btn-success">提交菜品</button>
                </div>
            </div>
        </div>
        
        <!-- 后台管理界面 -->
        <div id="backend" class="section">
            <h2>后台管理界面</h2>
            
            <!-- 管理员登录区域 -->
            <div id="admin-auth">
                <h3>管理员登录</h3>
                <div class="form-group">
                    <label for="admin-username">账号:</label>
                    <input type="text" id="admin-username" placeholder="请输入管理员账号">
                    <div id="admin-username-error" class="error-message hidden">请输入管理员账号</div>
                </div>
                <div class="form-group">
                    <label for="admin-password">密码:</label>
                    <input type="password" id="admin-password" placeholder="请输入密码">
                    <div id="admin-password-error" class="error-message hidden">请输入密码</div>
                </div>
                <button id="enter-backend" class="btn btn-primary">登录后台</button>
            </div>
            
            <!-- 后台管理区域 -->
            <div id="management-area" class="hidden">
                <div class="tabs">
                    <div class="tab active" data-tab="menu-management">菜单管理</div>
                    <div class="tab" data-tab="payment-management">支付管理</div>
                    <div class="tab" data-tab="preparation-management">备餐管理</div>
                </div>
                
                <!-- 菜单管理 -->
                <div id="menu-management" class="tab-content">
                    <h3>菜单管理</h3>
                    <div class="form-group">
                        <label for="dish-name">菜品名称:</label>
                        <input type="text" id="dish-name" placeholder="请输入菜品名称">
                        <div id="dish-name-error" class="error-message hidden">请输入菜品名称</div>
                    </div>
                    <div class="form-group">
                        <label for="dish-price">菜品价格:</label>
                        <input type="number" id="dish-price" placeholder="请输入菜品价格" min="0" step="0.01">
                        <div id="dish-price-error" class="error-message hidden">请输入有效的菜品价格</div>
                    </div>
                    <button id="add-dish" class="btn btn-primary">添加菜品</button>
                    
                    <h3>当前菜单</h3>
                    <table id="dish-list">
                        <thead>
                            <tr>
                                <th>菜品名称</th>
                                <th>价格</th>
                                <th>操作</th>
                            </tr>
                        </thead>
                        <tbody>
                            <!-- 菜品列表将通过JavaScript动态添加 -->
                        </tbody>
                    </table>
                </div>
                
                <!-- 支付管理 -->
                <div id="payment-management" class="tab-content hidden">
                    <h3>支付管理</h3>
                    <table id="order-list">
                        <thead>
                            <tr>
                                <th>订单号</th>
                                <th>桌号</th>
                                <th>会员号</th>
                                <th>总金额</th>
                                <th>状态</th>
                                <th>操作</th>
                            </tr>
                        </thead>
                        <tbody>
                            <!-- 订单列表将通过JavaScript动态添加 -->
                        </tbody>
                    </table>
                    
                    <!-- 支付详情 -->
                    <div id="payment-details" class="hidden">
                        <h3>订单详情</h3>
                        <table id="order-items">
                            <thead>
                                <tr>
                                    <th>菜品名称</th>
                                    <th>价格</th>
                                    <th>数量</th>
                                </tr>
                            </thead>
                            <tbody>
                                <!-- 订单菜品将通过JavaScript动态添加 -->
                            </tbody>
                        </table>
                        
                        <h3>合计: <span id="order-total">0</span>元</h3>
                        
                        <h3>选择支付方式</h3>
                        <div class="payment-options">
                            <div class="payment-option" data-method="wechat">微信支付</div>
                            <div class="payment-option" data-method="alipay">支付宝支付</div>
                            <div class="payment-option" data-method="credit">信用卡支付</div>
                            <div class="payment-option" data-method="cash">现金支付</div>
                        </div>
                        
                        <button id="process-payment" class="btn btn-success">处理支付</button>
                    </div>
                    
                    <!-- 支付完成页面 -->
                    <div id="payment-complete" class="hidden">
                        <h3 class="status-paid">支付完成!</h3>
                        <p>对方已成功支付。</p>
                        <button id="print-receipt" class="btn btn-primary">打印小票</button>
                        
                        <div id="receipt" class="receipt hidden">
                            <h3>餐厅小票</h3>
                            <p>订单号: <span id="receipt-order-id"></span></p>
                            <p>桌号: <span id="receipt-table"></span></p>
                            <p>会员号: <span id="receipt-member"></span></p>
                            <hr>
                            <div id="receipt-items">
                                <!-- 小票内容将通过JavaScript动态添加 -->
                            </div>
                            <hr>
                            <p>总计: <span id="receipt-total"></span>元</p>
                            <p>支付方式: <span id="receipt-method"></span></p>
                            <p>感谢惠顾!</p>
                        </div>
                    </div>
                </div>
                
                <!-- 备餐管理 -->
                <div id="preparation-management" class="tab-content hidden">
                    <h3>备餐区</h3>
                    <div id="preparation-orders">
                        <!-- 备餐订单将通过JavaScript动态添加 -->
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- 注册会员模态框 -->
    <div id="register-modal" class="modal">
        <div class="modal-content">
            <h3>注册会员</h3>
            <div class="form-group">
                <label for="reg-member-id">会员号:</label>
                <input type="text" id="reg-member-id" placeholder="请输入会员号">
            </div>
            <div class="form-group">
                <label for="reg-member-name">姓名:</label>
                <input type="text" id="reg-member-name" placeholder="请输入姓名">
            </div>
            <div class="form-group">
                <label for="reg-member-phone">电话:</label>
                <input type="text" id="reg-member-phone" placeholder="请输入电话">
            </div>
            <button id="confirm-register" class="btn btn-success">确认注册</button>
            <button id="cancel-register" class="btn btn-danger">取消</button>
        </div>
    </div>

    <script>
        // 模拟数据
        let menuItems = [
            { id: 1, name: "宫保鸡丁", price: 38 },
            { id: 2, name: "麻婆豆腐", price: 28 },
            { id: 3, name: "水煮鱼", price: 58 },
            { id: 4, name: "回锅肉", price: 42 },
            { id: 5, name: "鱼香肉丝", price: 36 },
            { id: 6, name: "糖醋里脊", price: 45 }
        ];
        
        let orders = [];
        let currentOrder = {
            items: [],
            total: 0,
            memberId: "",
            tableNumber: "",
            status: "unpaid"
        };
        
        let preparationOrders = [];
        let members = [];
        
        // 默认管理员账号密码
        const ADMIN_USERNAME = "admin";
        const ADMIN_PASSWORD = "123456";
        
        // DOM元素
        const frontendSection = document.getElementById('frontend');
        const backendSection = document.getElementById('backend');
        const memberAuth = document.getElementById('member-auth');
        const orderingArea = document.getElementById('ordering-area');
        const adminAuth = document.getElementById('admin-auth');
        const managementArea = document.getElementById('management-area');
        const menuItemsContainer = document.getElementById('menu-items');
        const selectedItemsTable = document.querySelector('#selected-items tbody');
        const totalPriceElement = document.getElementById('total-price');
        const dishListTable = document.querySelector('#dish-list tbody');
        const orderListTable = document.querySelector('#order-list tbody');
        const preparationOrdersContainer = document.getElementById('preparation-orders');
        const registerModal = document.getElementById('register-modal');
        
        // 初始化函数
        function init() {
            // 渲染菜单
            renderMenuItems();
            
            // 渲染菜品列表（后台）
            renderDishList();
            
            // 绑定事件
            bindEvents();
        }
        
        // 渲染菜单项
        function renderMenuItems() {
            menuItemsContainer.innerHTML = '';
            menuItems.forEach(item => {
                const menuItemElement = document.createElement('div');
                menuItemElement.className = 'menu-item';
                menuItemElement.innerHTML = `
                    <h4>${item.name}</h4>
                    <p>${item.price}元</p>
                `;
                menuItemElement.addEventListener('click', () => addToOrder(item));
                menuItemsContainer.appendChild(menuItemElement);
            });
        }
        
        // 渲染菜品列表（后台）
        function renderDishList() {
            dishListTable.innerHTML = '';
            menuItems.forEach(item => {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td>${item.name}</td>
                    <td>${item.price}元</td>
                    <td>
                        <button class="btn btn-danger delete-dish" data-id="${item.id}">删除</button>
                    </td>
                `;
                dishListTable.appendChild(row);
            });
            
            // 绑定删除按钮事件
            document.querySelectorAll('.delete-dish').forEach(button => {
                button.addEventListener('click', function() {
                    const dishId = parseInt(this.getAttribute('data-id'));
                    deleteDish(dishId);
                });
            });
        }
        
        // 绑定事件
        function bindEvents() {
            // 前台进入按钮
            document.getElementById('enter-frontend').addEventListener('click', enterFrontend);
            
            // 注册会员按钮
            document.getElementById('register-member').addEventListener('click', showRegisterModal);
            
            // 确认注册按钮
            document.getElementById('confirm-register').addEventListener('click', confirmRegister);
            
            // 取消注册按钮
            document.getElementById('cancel-register').addEventListener('click', hideRegisterModal);
            
            // 提交订单按钮
            document.getElementById('submit-order').addEventListener('click', submitOrder);
            
            // 后台登录按钮
            document.getElementById('enter-backend').addEventListener('click', enterBackend);
            
            // 添加菜品按钮
            document.getElementById('add-dish').addEventListener('click', addDish);
            
            // 标签切换
            document.querySelectorAll('.tab').forEach(tab => {
                tab.addEventListener('click', function() {
                    // 移除所有active类
                    document.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
                    // 隐藏所有标签内容
                    document.querySelectorAll('.tab-content').forEach(c => c.classList.add('hidden'));
                    
                    // 激活当前标签
                    this.classList.add('active');
                    // 显示对应内容
                    const tabId = this.getAttribute('data-tab');
                    document.getElementById(tabId).classList.remove('hidden');
                    
                    // 如果是支付管理标签，刷新订单列表
                    if (tabId === 'payment-management') {
                        renderOrderList();
                    }
                    
                    // 如果是备餐管理标签，刷新备餐订单
                    if (tabId === 'preparation-management') {
                        renderPreparationOrders();
                    }
                });
            });
            
            // 支付方式选择
            document.querySelectorAll('.payment-option').forEach(option => {
                option.addEventListener('click', function() {
                    // 移除所有选中状态
                    document.querySelectorAll('.payment-option').forEach(o => o.classList.remove('selected'));
                    // 设置当前选中
                    this.classList.add('selected');
                });
            });
            
            // 处理支付按钮
            document.getElementById('process-payment').addEventListener('click', processPayment);
            
            // 打印小票按钮
            document.getElementById('print-receipt').addEventListener('click', printReceipt);
        }
        
        // 显示注册模态框
        function showRegisterModal() {
            registerModal.style.display = 'flex';
        }
        
        // 隐藏注册模态框
        function hideRegisterModal() {
            registerModal.style.display = 'none';
            // 清空表单
            document.getElementById('reg-member-id').value = '';
            document.getElementById('reg-member-name').value = '';
            document.getElementById('reg-member-phone').value = '';
        }
        
        // 确认注册
        function confirmRegister() {
            const memberId = document.getElementById('reg-member-id').value.trim();
            const name = document.getElementById('reg-member-name').value.trim();
            const phone = document.getElementById('reg-member-phone').value.trim();
            
            if (!memberId || !name || !phone) {
                alert('请填写完整的会员信息！');
                return;
            }
            
            // 检查会员号是否已存在
            if (members.some(member => member.id === memberId)) {
                alert('该会员号已存在！');
                return;
            }
            
            // 添加会员
            members.push({
                id: memberId,
                name: name,
                phone: phone,
                registerTime: new Date().toLocaleString()
            });
            
            // 更新前台会员号输入框
            document.getElementById('member-id').value = memberId;
            
            // 隐藏模态框
            hideRegisterModal();
            
            alert(`会员 ${name} 注册成功！会员号为：${memberId}`);
        }
        
        // 进入前台
        function enterFrontend() {
            const memberId = document.getElementById('member-id').value.trim();
            const tableNumber = document.getElementById('table-number').value.trim();
            let hasError = false;
            
            // 清除之前的错误信息
            document.getElementById('member-id-error').classList.add('hidden');
            document.getElementById('table-number-error').classList.add('hidden');
            
            if (!memberId) {
                document.getElementById('member-id-error').classList.remove('hidden');
                hasError = true;
            }
            
            if (!tableNumber) {
                document.getElementById('table-number-error').classList.remove('hidden');
                hasError = true;
            }
            
            if (hasError) return;
            
            currentOrder.memberId = memberId;
            currentOrder.tableNumber = tableNumber;
            
            memberAuth.classList.add('hidden');
            orderingArea.classList.remove('hidden');
        }
        
        // 添加菜品到订单
        function addToOrder(item) {
            // 检查是否已存在该菜品
            const existingItem = currentOrder.items.find(i => i.id === item.id);
            
            if (existingItem) {
                existingItem.quantity += 1;
            } else {
                currentOrder.items.push({
                    id: item.id,
                    name: item.name,
                    price: item.price,
                    quantity: 1
                });
            }
            
            // 更新总价
            updateTotalPrice();
            // 更新已选菜品列表
            renderSelectedItems();
        }
        
        // 更新总价
        function updateTotalPrice() {
            currentOrder.total = currentOrder.items.reduce((sum, item) => sum + (item.price * item.quantity), 0);
            totalPriceElement.textContent = currentOrder.total;
        }
        
        // 渲染已选菜品
        function renderSelectedItems() {
            selectedItemsTable.innerHTML = '';
            
            currentOrder.items.forEach(item => {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td>${item.name}</td>
                    <td>${item.price}元</td>
                    <td>${item.quantity}</td>
                    <td>
                        <button class="btn btn-danger remove-item" data-id="${item.id}">删除</button>
                    </td>
                `;
                selectedItemsTable.appendChild(row);
            });
            
            // 绑定删除按钮事件
            document.querySelectorAll('.remove-item').forEach(button => {
                button.addEventListener('click', function() {
                    const itemId = parseInt(this.getAttribute('data-id'));
                    removeFromOrder(itemId);
                });
            });
        }
        
        // 从订单中移除菜品
        function removeFromOrder(itemId) {
            currentOrder.items = currentOrder.items.filter(item => item.id !== itemId);
            updateTotalPrice();
            renderSelectedItems();
        }
        
        // 提交订单
        function submitOrder() {
            if (currentOrder.items.length === 0) {
                alert('请先选择菜品！');
                return;
            }
            
            // 生成订单ID
            const orderId = 'ORD' + Date.now();
            
            // 创建订单副本
            const order = {
                id: orderId,
                items: [...currentOrder.items],
                total: currentOrder.total,
                memberId: currentOrder.memberId,
                tableNumber: currentOrder.tableNumber,
                status: 'unpaid'
            };
            
            // 添加到订单列表
            orders.push(order);
            
            // 添加到备餐区
            preparationOrders.push({
                id: orderId,
                items: [...currentOrder.items],
                tableNumber: currentOrder.tableNumber,
                status: 'preparing'
            });
            
            // 重置当前订单
            currentOrder.items = [];
            currentOrder.total = 0;
            updateTotalPrice();
            renderSelectedItems();
            
            alert('订单提交成功！');
            
            // 显示通知（模拟手机消息）
            showNotification(`您的订单已提交，正在准备中。`);
        }
        
        // 进入后台
        function enterBackend() {
            const username = document.getElementById('admin-username').value.trim();
            const password = document.getElementById('admin-password').value.trim();
            let hasError = false;
            
            // 清除之前的错误信息
            document.getElementById('admin-username-error').classList.add('hidden');
            document.getElementById('admin-password-error').classList.add('hidden');
            
            if (!username) {
                document.getElementById('admin-username-error').classList.remove('hidden');
                hasError = true;
            }
            
            if (!password) {
                document.getElementById('admin-password-error').classList.remove('hidden');
                hasError = true;
            }
            
            if (hasError) return;
            
            if (username === ADMIN_USERNAME && password === ADMIN_PASSWORD) {
                adminAuth.classList.add('hidden');
                managementArea.classList.remove('hidden');
                
                // 默认显示菜单管理
                document.querySelectorAll('.tab-content').forEach(c => c.classList.add('hidden'));
                document.getElementById('menu-management').classList.remove('hidden');
            } else {
                alert('账号或密码错误，禁止进入！');
            }
        }
        
        // 添加菜品
        function addDish() {
            const name = document.getElementById('dish-name').value.trim();
            const price = parseFloat(document.getElementById('dish-price').value);
            let hasError = false;
            
            // 清除之前的错误信息
            document.getElementById('dish-name-error').classList.add('hidden');
            document.getElementById('dish-price-error').classList.add('hidden');
            
            if (!name) {
                document.getElementById('dish-name-error').classList.remove('hidden');
                hasError = true;
            }
            
            if (!price || price <= 0) {
                document.getElementById('dish-price-error').classList.remove('hidden');
                hasError = true;
            }
            
            if (hasError) return;
            
            // 生成菜品ID
            const id = menuItems.length > 0 ? Math.max(...menuItems.map(item => item.id)) + 1 : 1;
            
            // 添加菜品
            menuItems.push({ id, name, price });
            
            // 更新菜单显示
            renderMenuItems();
            renderDishList();
            
            // 清空表单
            document.getElementById('dish-name').value = '';
            document.getElementById('dish-price').value = '';
            
            alert('菜品添加成功！');
        }
        
        // 删除菜品
        function deleteDish(dishId) {
            if (confirm('确定要删除这个菜品吗？')) {
                menuItems = menuItems.filter(item => item.id !== dishId);
                renderMenuItems();
                renderDishList();
                alert('菜品删除成功！');
            }
        }
        
        // 渲染订单列表
        function renderOrderList() {
            orderListTable.innerHTML = '';
            
            orders.forEach(order => {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td>${order.id}</td>
                    <td>${order.tableNumber}</td>
                    <td>${order.memberId}</td>
                    <td>${order.total}元</td>
                    <td class="${order.status === 'paid' ? 'status-paid' : 'status-unpaid'}">
                        ${order.status === 'paid' ? '已支付' : '去支付'}
                    </td>
                    <td>
                        <button class="btn btn-primary view-order" data-id="${order.id}">查看</button>
                    </td>
                `;
                orderListTable.appendChild(row);
            });
            
            // 绑定查看订单按钮事件
            document.querySelectorAll('.view-order').forEach(button => {
                button.addEventListener('click', function() {
                    const orderId = this.getAttribute('data-id');
                    viewOrderDetails(orderId);
                });
            });
        }
        
        // 查看订单详情
        function viewOrderDetails(orderId) {
            const order = orders.find(o => o.id === orderId);
            if (!order) return;
            
            // 显示支付详情区域
            document.getElementById('payment-details').classList.remove('hidden');
            document.getElementById('payment-complete').classList.add('hidden');
            
            // 渲染订单菜品
            const orderItemsTable = document.querySelector('#order-items tbody');
            orderItemsTable.innerHTML = '';
            
            order.items.forEach(item => {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td>${item.name}</td>
                    <td>${item.price}元</td>
                    <td>${item.quantity}</td>
                `;
                orderItemsTable.appendChild(row);
            });
            
            // 更新总价
            document.getElementById('order-total').textContent = order.total;
            
            // 存储当前查看的订单ID
            document.getElementById('payment-details').setAttribute('data-order-id', orderId);
        }
        
        // 处理支付
        function processPayment() {
            const orderId = document.getElementById('payment-details').getAttribute('data-order-id');
            const order = orders.find(o => o.id === orderId);
            if (!order) return;
            
            // 检查是否选择了支付方式
            const selectedMethod = document.querySelector('.payment-option.selected');
            if (!selectedMethod) {
                alert('请选择支付方式！');
                return;
            }
            
            const paymentMethod = selectedMethod.getAttribute('data-method');
            
            // 模拟支付过程
            alert(`正在处理${getPaymentMethodName(paymentMethod)}...`);
            
            // 更新订单状态
            order.status = 'paid';
            
            // 隐藏支付详情，显示支付完成页面
            document.getElementById('payment-details').classList.add('hidden');
            document.getElementById('payment-complete').classList.remove('hidden');
            
            // 更新小票信息
            document.getElementById('receipt-order-id').textContent = order.id;
            document.getElementById('receipt-table').textContent = order.tableNumber;
            document.getElementById('receipt-member').textContent = order.memberId;
            document.getElementById('receipt-total').textContent = order.total;
            document.getElementById('receipt-method').textContent = getPaymentMethodName(paymentMethod);
            
            // 渲染小票菜品
            const receiptItems = document.getElementById('receipt-items');
            receiptItems.innerHTML = '';
            
            order.items.forEach(item => {
                const itemElement = document.createElement('div');
                itemElement.innerHTML = `${item.name} x${item.quantity} - ${item.price * item.quantity}元`;
                receiptItems.appendChild(itemElement);
            });
            
            // 更新订单列表
            renderOrderList();
        }
        
        // 获取支付方式名称
        function getPaymentMethodName(method) {
            switch(method) {
                case 'wechat': return '微信支付';
                case 'alipay': return '支付宝支付';
                case 'credit': return '信用卡支付';
                case 'cash': return '现金支付';
                default: return '未知支付方式';
            }
        }
        
        // 打印小票
        function printReceipt() {
            document.getElementById('receipt').classList.remove('hidden');
            window.print();
            document.getElementById('receipt').classList.add('hidden');
        }
        
        // 渲染备餐订单
        function renderPreparationOrders() {
            preparationOrdersContainer.innerHTML = '';
            
            if (preparationOrders.length === 0) {
                preparationOrdersContainer.innerHTML = '<p>暂无备餐订单</p>';
                return;
            }
            
            preparationOrders.forEach(order => {
                const orderElement = document.createElement('div');
                orderElement.className = 'order-item';
                orderElement.innerHTML = `
                    <h4>订单号: ${order.id} - 桌号: ${order.tableNumber}</h4>
                    <ul>
                        ${order.items.map(item => `<li>${item.name} x${item.quantity}</li>`).join('')}
                    </ul>
                    <p>状态: ${order.status === 'preparing' ? '准备中' : '已完成'}</p>
                    ${order.status === 'preparing' ? 
                        `<button class="btn btn-success complete-order" data-id="${order.id}">完成备餐</button>` : 
                        `<span class="status-paid">已出餐</span>`
                    }
                `;
                preparationOrdersContainer.appendChild(orderElement);
            });
            
            // 绑定完成备餐按钮事件
            document.querySelectorAll('.complete-order').forEach(button => {
                button.addEventListener('click', function() {
                    const orderId = this.getAttribute('data-id');
                    completePreparation(orderId);
                });
            });
        }
        
        // 完成备餐
        function completePreparation(orderId) {
            const orderIndex = preparationOrders.findIndex(o => o.id === orderId);
            if (orderIndex !== -1) {
                preparationOrders[orderIndex].status = 'completed';
                
                // 显示通知（模拟手机消息）
                const tableNumber = preparationOrders[orderIndex].tableNumber;
                showNotification(`您的订单已就绪，请到${tableNumber}号桌取餐。`);
                
                // 重新渲染备餐订单
                renderPreparationOrders();
            }
        }
        
        // 显示通知
        function showNotification(message) {
            const notification = document.createElement('div');
            notification.className = 'notification';
            notification.textContent = message;
            document.body.appendChild(notification);
            
            // 3秒后移除通知
            setTimeout(() => {
                notification.remove();
            }, 3000);
        }
        
        // 初始化应用
        init();
    </script>
</body>
</html>
