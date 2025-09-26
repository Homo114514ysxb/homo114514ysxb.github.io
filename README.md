<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>五宁餐厅系统</title>
    <style>
        /* 基础样式 */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', sans-serif;
        }
        body {
            padding: 20px;
            background: #f0f2f5;
        }
        .container {
            max-width: 1200px;
            margin: 0 auto;
        }
        .page {
            display: none;
            padding: 20px;
            background: white;
            border-radius: 8px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
        }
        .page.active {
            display: block;
        }
        .form-group {
            margin: 15px 0;
        }
        label {
            display: block;
            margin-bottom: 5px;
            font-weight: 500;
        }
        input {
            width: 100%;
            padding: 8px;
            border: 1px solid #ddd;
            border-radius: 4px;
        }
        button {
            padding: 8px 16px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            margin: 5px;
        }
        .btn-primary {
            background: #4285f4;
            color: white;
        }
        .btn-secondary {
            background: #f1f3f4;
            color: #333;
        }
        .btn-danger {
            background: #ea4335;
            color: white;
        }
        .list-item {
            padding: 10px;
            border-bottom: 1px dashed #eee;
        }
        .status {
            display: inline-block;
            padding: 3px 8px;
            border-radius: 12px;
            font-size: 0.8em;
        }
        .status-pending {
            background: #fbbc05;
            color: white;
        }
        .status-ready {
            background: #34a853;
            color: white;
        }
        .status-paid {
            background: #4285f4;
            color: white;
        }
        .cart-item {
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 10px;
            margin: 5px 0;
            background: #f9f9f9;
            border-radius: 4px;
        }
        .payment-methods {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
            gap: 10px;
            margin: 20px 0;
        }
        .payment-option {
            padding: 15px;
            border: 1px solid #ddd;
            border-radius: 4px;
            text-align: center;
            cursor: pointer;
        }
        .payment-option.selected {
            border-color: #4285f4;
            background: #e8f0fe;
        }
        .receipt {
            margin-top: 20px;
            padding: 15px;
            border: 1px dashed #ccc;
            font-family: monospace;
        }
    </style>
</head>
<body>
    <!-- 登录页面 -->
    <div id="loginPage" class="page active container">
        <h2>五宁餐厅登录</h2>
        <div class="form-group">
            <h3>顾客登录</h3>
            <input type="number" id="memberCode" placeholder="6位会员码" maxlength="6">
            <input type="text" id="seatNumber" placeholder="座位号">
            <button class="btn-primary" onclick="frontLogin()">进入前台</button>
        </div>
        <div class="form-group">
            <h3>管理员登录</h3>
            <input type="text" id="adminUser" placeholder="账号">
            <input type="password" id="adminPwd" placeholder="密码">
            <button class="btn-primary" onclick="adminLogin()">进入后台</button>
        </div>
    </div>

    <!-- 前台页面 -->
    <div id="frontPage" class="page container">
        <h2>五宁餐厅</h2>
        <p>当前会员：<span id="currentMember"></span> | 座位号：<span id="currentSeat"></span></p>
        
        <h3>菜单</h3>
        <div id="menuList"></div>

        <h3>购物车</h3>
        <div id="cartList"></div>
        <p>合计：¥<span id="totalPrice">0</span></p>
        <button class="btn-primary" onclick="submitOrder()">提交订单</button>
        <button class="btn-secondary" onclick="logout()">返回登录</button>
    </div>

    <!-- 后台页面 -->
    <div id="adminPage" class="page container">
        <h2>后台管理</h2>
        <div class="form-group">
            <h3>菜品管理</h3>
            <input type="text" id="dishName" placeholder="菜品名称">
            <input type="number" id="dishPrice" step="0.01" placeholder="价格">
            <button class="btn-primary" onclick="addDish()">添加菜品</button>
            <div id="dishList"></div>
        </div>

        <div class="form-group">
            <h3>订单管理</h3>
            <div id="orderList"></div>
        </div>

        <button class="btn-danger" onclick="logout()">退出登录</button>
    </div>

    <!-- 支付页面 -->
    <div id="paymentPage" class="page container">
        <h2>订单支付</h2>
        <button class="btn-secondary" onclick="showPage('adminPage')">返回后台</button>
        <div id="paymentDetails"></div>
        <div class="payment-methods" id="paymentMethods"></div>
        <button class="btn-primary" onclick="confirmPayment()">确认支付</button>
        <div class="receipt" id="receipt"></div>
    </div>

    <script>
        // 数据层
        const dataStore = {
            members: [
                { code: 123123, name: '张三' },
                { code: 112233, name: '李四' }
            ],
            admin: { user: 'admin', pwd: '123456' },
            dishes: [
                { id: 1, name: '宫保鸡丁', price: 28.8 },
                { id: 2, name: '鱼香肉丝', price: 25.5 }
            ],
            orders: [],
            currentCart: [],
            currentPaymentOrder: null
        };

        // 视图层
        function renderMenu() {
            const menuEl = document.getElementById('menuList');
            menuEl.innerHTML = dataStore.dishes.map(dish => `
                <div class="list-item" onclick="addToCart(${dish.id})">
                    ${dish.name} - ¥${dish.price.toFixed(2)}
                </div>
            `).join('');
        }

        function renderCart() {
            const cartEl = document.getElementById('cartList');
            const totalEl = document.getElementById('totalPrice');
            const cart = dataStore.currentCart;
            
            if (cart.length === 0) {
                cartEl.innerHTML = '<p>购物车为空</p>';
                totalEl.textContent = '0';
                return;
            }

            const total = cart.reduce((sum, item) => sum + item.price * item.quantity, 0);
            cartEl.innerHTML = cart.map(item => `
                <div class="cart-item">
                    <span>${item.name} x${item.quantity}</span>
                    <span>¥${(item.price * item.quantity).toFixed(2)}</span>
                    <button class="btn-danger" onclick="removeFromCart(${item.id})">删除</button>
                </div>
            `).join('');
            totalEl.textContent = total.toFixed(2);
        }

        function renderAdminDishList() {
            const dishEl = document.getElementById('dishList');
            dishEl.innerHTML = dataStore.dishes.map(dish => `
                <div class="list-item">
                    ${dish.name} - ¥${dish.price.toFixed(2)}
                    <button class="btn-danger" onclick="deleteDish(${dish.id})">删除</button>
                </div>
            `).join('');
        }

        function renderAdminOrderList() {
            const orderEl = document.getElementById('orderList');
            orderEl.innerHTML = dataStore.orders.map(order => `
                <div class="list-item">
                    <h4>订单 ${order.id}</h4>
                    <p>会员：${order.member.name} | 座位：${order.seat}</p>
                    <p>菜品：${order.items.map(i => `${i.name} x${i.quantity}`).join('、')}</p>
                    <p>总价：¥${order.total.toFixed(2)}</p>
                    <div>
                        ${order.status === 'pending' ? 
                            `<button class="btn-primary" onclick="markAsReady(${order.id})">标记为已准备</button>` : 
                            `<span class="status status-ready">已准备</span>`
                        }
                        ${order.status === 'ready' && !order.paid ? 
                            `<button class="btn-primary" onclick="showPaymentPage(${order.id})">去支付</button>` : 
                            order.paid ? `<span class="status status-paid">已支付</span>` : ''
                        }
                    </div>
                </div>
            `).join('');
        }

        function renderPaymentPage(orderId) {
            const order = dataStore.orders.find(o => o.id === orderId);
            if (!order) return;

            dataStore.currentPaymentOrder = order;
            const paymentDetails = document.getElementById('paymentDetails');
            const paymentMethods = document.getElementById('paymentMethods');
            
            // 渲染订单详情
            paymentDetails.innerHTML = `
                <h3>订单详情</h3>
                ${order.items.map(item => `
                    <div class="list-item">
                        ${item.name} x${item.quantity} - ¥${(item.price * item.quantity).toFixed(2)}
                    </div>
                `).join('')}
                <p>订单总额：¥${order.total.toFixed(2)}</p>
            `;

            // 渲染支付方式
            paymentMethods.innerHTML = `
                <div class="payment-option" onclick="selectPayment('wechat')">
                    <h4>微信支付</h4>
                </div>
                <div class="payment-option" onclick="selectPayment('alipay')">
                    <h4>支付宝支付</h4>
                </div>
                <div class="payment-option" onclick="selectPayment('credit')">
                    <h4>信用卡支付</h4>
                </div>
                <div class="payment-option" onclick="selectPayment('cash')">
                    <h4>现金支付</h4>
                </div>
            `;

            showPage('paymentPage');
        }

        // 业务逻辑
        function frontLogin() {
            const code = document.getElementById('memberCode').value;
            const seat = document.getElementById('seatNumber').value;
            
            if (!code || code.length !== 6 || isNaN(code)) {
                alert('请输入有效的6位会员码');
                return;
            }
            
            if (!seat) {
                alert('请输入座位号');
                return;
            }
            
            const member = dataStore.members.find(m => m.code === Number(code));
            if (!member) {
                alert('会员不存在');
                return;
            }
            
            dataStore.currentCart = [];
            document.getElementById('currentMember').textContent = member.name;
            document.getElementById('currentSeat').textContent = seat;
            showPage('frontPage');
            renderMenu();
            renderCart();
        }

        function adminLogin() {
            const user = document.getElementById('adminUser').value;
            const pwd = document.getElementById('adminPwd').value;
            
            if (user !== dataStore.admin.user || pwd !== dataStore.admin.pwd) {
                alert('账号或密码错误');
                return;
            }
            
            showPage('adminPage');
            renderAdminDishList();
            renderAdminOrderList();
        }

        function addToCart(dishId) {
            const dish = dataStore.dishes.find(d => d.id === dishId);
            const cart = dataStore.currentCart;
            
            const existing = cart.find(item => item.id === dishId);
            if (existing) {
                existing.quantity++;
            } else {
                cart.push({ ...dish, quantity: 1 });
            }
            renderCart();
        }

        function removeFromCart(dishId) {
            dataStore.currentCart = dataStore.currentCart.filter(item => item.id !== dishId);
            renderCart();
        }

        function submitOrder() {
            const cart = dataStore.currentCart;
            if (cart.length === 0) {
                alert('购物车为空');
                return;
            }
            
            const member = {
                code: Number(document.getElementById('currentMember').nextSibling.textContent),
                name: document.getElementById('currentMember').textContent
            };
            const seat = document.getElementById('currentSeat').textContent;
            const total = cart.reduce((sum, item) => sum + item.price * item.quantity, 0);
            const order = {
                id: Date.now(),
                member,
                seat,
                items: [...cart],
                total,
                status: 'pending',
                paid: false,
                paymentMethod: ''
            };
            
            dataStore.orders.push(order);
            dataStore.currentCart = [];
            renderCart();
            alert('订单提交成功');
            renderAdminOrderList();
        }

        function addDish() {
            const name = document.getElementById('dishName').value;
            const price = parseFloat(document.getElementById('dishPrice').value);
            
            if (!name || isNaN(price) || price <= 0) {
                alert('请输入有效的菜品信息');
                return;
            }
            
            const id = dataStore.dishes.length > 0 ? Math.max(...dataStore.dishes.map(d => d.id)) + 1 : 1;
            dataStore.dishes.push({ id, name, price });
            document.getElementById('dishName').value = '';
            document.getElementById('dishPrice').value = '';
            renderAdminDishList();
            renderMenu();
        }

        function deleteDish(dishId) {
            dataStore.dishes = dataStore.dishes.filter(d => d.id !== dishId);
            dataStore.currentCart = dataStore.currentCart.filter(item => item.id !== dishId);
            renderAdminDishList();
            renderCart();
        }

        function markAsReady(orderId) {
            const order = dataStore.orders.find(o => o.id === orderId);
            if (order) {
                order.status = 'ready';
                renderAdminOrderList();
                alert('订单已标记为准备完成');
            }
        }

        function showPaymentPage(orderId) {
            renderPaymentPage(orderId);
        }

        function selectPayment(method) {
            document.querySelectorAll('.payment-option').forEach(option => {
                option.classList.remove('selected');
            });
            event.currentTarget.classList.add('selected');
            dataStore.currentPaymentOrder.paymentMethod = method;
        }

        function confirmPayment() {
            const order = dataStore.currentPaymentOrder;
            if (!order) {
                alert('请选择需要支付的订单');
                return;
            }
            
            if (!order.paymentMethod) {
                alert('请选择支付方式');
                return;
            }
            
            order.paid = true;
            renderAdminOrderList();
            showReceipt(order);
        }

        function showReceipt(order) {
            const receiptEl = document.getElementById('receipt');
            receiptEl.innerHTML = `
                <h4>五宁餐厅 小票</h4>
                <p>订单号：${order.id}</p>
                <p>会员：${order.member.name} (${order.member.code})</p>
                <p>座位号：${order.seat}</p>
                <p>支付方式：${order.paymentMethod}</p>
                <p>菜品：${order.items.map(item => `${item.name} x${item.quantity}`).join('、')}</p>
                <p>总金额：¥${order.total.toFixed(2)}</p>
                <p>支付时间：${new Date().toLocaleString()}</p>
            `;
            receiptEl.style.display = 'block';
            setTimeout(() => {
                receiptEl.style.display = 'none';
                showPage('adminPage');
            }, 3000);
        }

        // 通用功能
        function showPage(pageId) {
            document.querySelectorAll('.page').forEach(page => page.classList.remove('active'));
            document.getElementById(pageId).classList.add('active');
        }

        function logout() {
            document.querySelectorAll('input').forEach(input => input.value = '');
            showPage('loginPage');
        }

        // 初始化
        renderMenu();
    </script>
</body>
</html>
