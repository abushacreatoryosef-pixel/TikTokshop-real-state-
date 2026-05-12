<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WDS - Cinematic Real Estate</title>
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700&family=Roboto:wght@300;500&display=swap" rel="stylesheet">
    <style>
        :root {
            --neon-blue: #00f2ea;
            --neon-pink: #ff0050;
            --dark-bg: #050505;
            --card-bg: #121212;
        }

        body {
            font-family: 'Roboto', sans-serif;
            background-color: var(--dark-bg);
            color: white;
            margin: 0;
            padding-bottom: 80px;
        }

        /* Neon Alert System */
        .custom-alert {
            position: fixed;
            top: -100px;
            left: 50%;
            transform: translateX(-50%);
            width: 90%;
            max-width: 400px;
            background: linear-gradient(135deg, #1a1a1a, #000);
            border: 2px solid var(--neon-blue);
            padding: 20px;
            border-radius: 15px;
            text-align: center;
            z-index: 10000;
            transition: 0.5s ease-in-out;
            box-shadow: 0 0 20px var(--neon-blue);
        }

        .custom-alert.show { top: 20px; }

        /* Balance Display */
        .header-dashboard {
            background: linear-gradient(180deg, #111, #000);
            padding: 30px 20px;
            border-bottom: 2px solid var(--neon-blue);
            text-align: center;
            position: sticky;
            top: 0;
            z-index: 100;
        }

        .balance-card {
            background: rgba(255, 255, 255, 0.05);
            padding: 20px;
            border-radius: 20px;
            border: 1px dashed var(--neon-blue);
            display: inline-block;
            min-width: 250px;
        }

        .balance-value {
            font-family: 'Orbitron', sans-serif;
            font-size: 2.2rem;
            color: var(--neon-blue);
            text-shadow: 0 0 10px var(--neon-blue);
        }

        /* Products - Real Estate Style */
        .product-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
            padding: 20px;
        }

        .product-card {
            background: var(--card-bg);
            border-radius: 15px;
            overflow: hidden;
            border: 1px solid #333;
            transition: 0.3s;
        }

        .product-card img {
            width: 100%;
            height: 120px;
            object-fit: cover;
            border-bottom: 1px solid var(--neon-blue);
        }

        .product-details { padding: 10px; text-align: center; }
        .product-details b { color: var(--neon-blue); font-size: 0.9rem; }
        .product-details p { font-size: 0.7rem; margin: 5px 0; color: #aaa; }

        /* Navigation */
        .bottom-nav {
            position: fixed;
            bottom: 0;
            width: 100%;
            background: #000;
            display: flex;
            justify-content: space-around;
            padding: 15px 0;
            border-top: 1px solid var(--neon-blue);
            z-index: 1000;
        }

        .nav-item { color: #666; font-size: 0.7rem; text-align: center; cursor: pointer; }
        .nav-item.active { color: var(--neon-blue); font-weight: bold; }

        .btn-action {
            background: linear-gradient(90deg, var(--neon-pink), var(--neon-blue));
            border: none; color: white; padding: 10px; border-radius: 8px;
            font-weight: bold; cursor: pointer; width: 100%; font-size: 0.8rem;
        }

        .hidden { display: none !important; }
        input, select { width: 90%; padding: 12px; margin: 10px 0; background: #111; border: 1px solid #333; color: white; border-radius: 8px; }

        /* Bonus Gift */
        #bonus-btn {
            position: fixed;
            top: 100px;
            right: 20px;
            font-size: 40px;
            cursor: pointer;
            z-index: 500;
            animation: bounce 2s infinite;
        }
        @keyframes bounce { 0%, 100% { transform: translateY(0); } 50% { transform: translateY(-10px); } }

    </style>
</head>
<body>

    <div id="customAlert" class="custom-alert">
        <h4 id="alertTitle" style="margin:0; color:var(--neon-blue)">Notification</h4>
        <p id="alertMsg" style="font-size: 0.9rem;"></p>
    </div>

    <div id="page-auth" style="padding: 100px 20px; text-align: center;">
        <h1 style="font-family:'Orbitron'; color:var(--neon-blue)">WDS LOGIN</h1>
        <input type="text" id="auth-ph" placeholder="Phone Number">
        <input type="password" id="auth-pw" placeholder="Password">
        <button class="btn-action" onclick="processAuth()">ENTER SYSTEM</button>
    </div>

    <div id="page-dashboard" class="hidden">
        <div id="bonus-btn" onclick="claimWelcomeBonus()">🎁</div>
        <div class="header-dashboard">
            <div class="balance-card">
                <small id="lang-bal-label">Current Balance</small><br>
                <span class="balance-value">ETB <span id="main-balance">0.00</span></span>
            </div>
        </div>
        <div class="product-grid" id="product-container"></div>
    </div>

    <div id="page-task" class="hidden" style="padding: 20px;">
        <h2 id="lang-task-title">Daily Income</h2>
        <div id="active-tasks"></div>
    </div>

    <div id="page-finance" class="hidden" style="padding: 20px;">
        <h3>Recharge</h3>
        <p style="font-size: 0.8rem; color: #aaa;">Copy Account: <b style="color:var(--neon-blue)" onclick="copyToClipboard('1000705884987')">1000705884987</b></p>
        <input type="number" id="recharge-amt" placeholder="Amount">
        <input type="text" id="recharge-ft" placeholder="FT Number">
        <button class="btn-action" onclick="submitDeposit()">Submit Recharge</button>
        <hr style="margin: 20px 0; border: 0.1px solid #333;">
        <h3>Withdrawal</h3>
        <input type="number" id="wd-amt" placeholder="Min 200 ETB">
        <input type="text" id="wd-name" placeholder="Full Name">
        <input type="text" id="wd-acc" placeholder="CBE Account Number">
        <button class="btn-action" style="background:var(--neon-pink)" onclick="handleWithdraw()">Withdraw Funds</button>
    </div>

    <div id="page-settings" class="hidden" style="padding: 20px;">
        <h3>Settings</h3>
        <button class="btn-action" style="background:#222; margin-bottom:10px;" onclick="copyInviteLink()">Copy Invite Link</button>
        <select id="lang-select" onchange="changeLanguage(this.value)">
            <option value="en">English</option>
            <option value="om">Afaan Oromoo</option>
            <option value="am">አማርኛ</option>
        </select>
        <button class="btn-action" style="background:#444;" onclick="logout()">Log Out</button>
    </div>

    <div id="page-admin" class="hidden" style="padding: 20px; background:#000;">
        <h2 style="color:lime">ADMIN CONTROL</h2>
        <div id="admin-user-list"></div>
    </div>

    <nav class="bottom-nav hidden" id="main-nav">
        <div class="nav-item active" onclick="navigate('dashboard', this)">🏠<br>Home</div>
        <div class="nav-item" onclick="navigate('task', this)">📋<br>Task</div>
        <div class="nav-item" onclick="navigate('finance', this)">💰<br>Finance</div>
        <div class="nav-item" onclick="navigate('settings', this)">⚙️<br>Settings</div>
        <div class="nav-item hidden" id="admin-nav" onclick="navigate('admin', this)">👑<br>Admin</div>
    </nav>

    <script>
        const BOT_TOKEN = "86449777708703665809:AAGLk0uCFBA96e4PaVDP2KVufX1hnGzVGpca";
        const CHAT_ID = "580905563319";
        const ADMIN_PH = "0975780412";

        let db = JSON.parse(localStorage.getItem('wds_data')) || { users: {} };
        let currentUser = localStorage.getItem('logged_in_user');

        const i18n = {
            en: { bal: "Current Balance", task: "Daily Tasks", welcome: "Welcome Bonus Claimed!" },
            om: { bal: "Herrega Ammaa", task: "Hojii Guyyaa", welcome: "Kennaa simannaa fudhatteetta!" },
            am: { bal: "የአሁኑ ሒሳብ", task: "የቀን ስራዎች", welcome: "የእንኳን ደህና መጡ ቦነስ ተቀብለዋል!" }
        };

        // --- Auth Logic ---
        function processAuth() {
            const ph = document.getElementById('auth-ph').value;
            const pw = document.getElementById('auth-pw').value;
            if(!ph || !pw) return showAlert("Error", "Please fill all fields");

            if(!db.users[ph]) {
                db.users[ph] = { ph, pw, balance: 0, plans: [], bonusUsed: false, lang: 'en', history: [], lastTask: {} };
            }

            if(db.users[ph].pw === pw) {
                currentUser = ph;
                localStorage.setItem('logged_in_user', ph);
                save();
                location.reload();
            } else {
                showAlert("Access Denied", "Wrong password!");
            }
        }

        // --- Navigation ---
        function navigate(page, el) {
            document.querySelectorAll('[id^="page-"]').forEach(p => p.classList.add('hidden'));
            document.getElementById('page-' + page).classList.remove('hidden');
            document.querySelectorAll('.nav-item').forEach(i => i.classList.remove('active'));
            el.classList.add('active');
            
            if(page === 'dashboard') renderProducts();
            if(page === 'task') renderTasks();
            if(page === 'admin') renderAdmin();
            updateBalanceUI();
        }

        function updateBalanceUI() {
            const u = db.users[currentUser];
            document.getElementById('main-balance').innerText = u.balance.toFixed(2);
            document.getElementById('lang-bal-label').innerText = i18n[u.lang].bal;
            if(u.bonusUsed) document.getElementById('bonus-btn').classList.add('hidden');
        }

        // --- Core Functions ---
        function renderProducts() {
            const cont = document.getElementById('product-container');
            cont.innerHTML = "";
            const u = db.users[currentUser];

            for(let i=1; i<=15; i++) {
                let dep = 1000 + (i-1)*1500;
                let daily = dep * 0.065;
                let isOwned = u.plans.includes('F'+i);

                cont.innerHTML += `
                    <div class="product-card">
                        <img src="https://images.unsplash.com/photo-1564013799919-ab600027ffc6?auto=format&fit=crop&w=400&q=80&sig=${i}">
                        <div class="product-details">
                            <b>Project F${i}</b>
                            <p>Deposit: ${dep} ETB</p>
                            <p>Daily: ${daily.toFixed(0)} ETB</p>
                            <button class="btn-action" style="${isOwned?'background:#333':''}" onclick="${isOwned?'':'buyPlan('+dep+',\'F'+i+'\')'}">
                                ${isOwned ? 'INVESTED ✅' : 'INVEST'}
                            </button>
                        </div>
                    </div>
                `;
            }
        }

        function buyPlan(cost, id) {
            const u = db.users[currentUser];
            if(u.balance < cost) return showAlert("Low Balance", "Please recharge your account first.");
            u.balance -= cost;
            u.plans.push(id);
            save();
            renderProducts();
            updateBalanceUI();
            showAlert("Success", id + " Investment started!");
        }

        function renderTasks() {
            const cont = document.getElementById('active-tasks');
            cont.innerHTML = "";
            const u = db.users[currentUser];
            if(u.plans.length === 0) cont.innerHTML = "<p>No active investments.</p>";

            u.plans.forEach(plan => {
                cont.innerHTML += `
                    <div style="background:#111; padding:15px; border-radius:10px; margin-bottom:10px; border-left:4px solid var(--neon-blue)">
                        <b>${plan} Daily Income</b>
                        <button class="btn-action" style="margin-top:10px" onclick="claimIncome('${plan}')">Claim</button>
                    </div>
                `;
            });
        }

        function claimIncome(planId) {
            const u = db.users[currentUser];
            const now = Date.now();
            const last = u.lastTask[planId] || 0;
            if(now - last < 24*60*60*1000) return showAlert("Locked", "Come back in 24 hours!");

            let idx = parseInt(planId.replace('F',''));
            let dep = 1000 + (idx-1)*1500;
            let profit = dep * 0.065;

            u.balance += profit;
            u.lastTask[planId] = now;
            save();
            updateBalanceUI();
            showAlert("Income Received", "ETB " + profit.toFixed(2) + " added to balance.");
        }

        function handleWithdraw() {
            const amt = parseFloat(document.getElementById('wd-amt').value);
            const u = db.users[currentUser];
            if(amt < 200) return showAlert("Limit", "Minimum withdrawal is 200 ETB");
            if(u.balance < amt) return showAlert("Error", "Insufficient funds in your account");

            u.balance -= amt;
            save();
            updateBalanceUI();
            sendTelegram(`💸 WITHDRAWAL\nUser: ${currentUser}\nAmount: ${amt}\nAccount: ${document.getElementById('wd-acc').value}`);
            showAlert("Successful", "Withdrawal request sent to Admin.");
        }

        function submitDeposit() {
            const amt = document.getElementById('recharge-amt').value;
            const ft = document.getElementById('recharge-ft').value;
            if(!amt || !ft) return showAlert("Incomplete", "Enter amount and FT number");
            sendTelegram(`💰 RECHARGE\nUser: ${currentUser}\nAmount: ${amt}\nFT: ${ft}`);
            showAlert("Pending", "Deposit submitted. Admin will verify soon.");
        }

        function claimWelcomeBonus() {
            const u = db.users[currentUser];
            if(u.bonusUsed) return;
            u.balance += 100;
            u.bonusUsed = true;
            save();
            updateBalanceUI();
            showAlert("Gift", i18n[u.lang].welcome);
        }

        // --- Admin Functions ---
        function renderAdmin() {
            const list = document.getElementById('admin-user-list');
            list.innerHTML = "";
            Object.values(db.users).forEach(u => {
                list.innerHTML += `
                    <div style="background:#111; padding:10px; margin:5px; border:1px solid #333;">
                        ${u.ph} | Bal: ${u.balance.toFixed(2)}<br>
                        <button onclick="adminAdjust('${u.ph}')">Set Balance</button>
                    </div>
                `;
            });
        }

        function adminAdjust(ph) {
            let newVal = prompt("Enter new balance for " + ph);
            if(newVal !== null) {
                db.users[ph].balance = parseFloat(newVal);
                save();
                renderAdmin();
            }
        }

        // --- Utils ---
        function save() { localStorage.setItem('wds_data', JSON.stringify(db)); }
        
        function showAlert(title, msg) {
            const a = document.getElementById('customAlert');
            document.getElementById('alertTitle').innerText = title;
            document.getElementById('alertMsg').innerText = msg;
            a.classList.add('show');
            setTimeout(() => a.classList.remove('show'), 3500);
        }

        function sendTelegram(msg) {
            fetch(`https://api.telegram.org/bot${BOT_TOKEN}/sendMessage?chat_id=${CHAT_ID}&text=${encodeURIComponent(msg)}`);
        }

        function changeLanguage(val) {
            db.users[currentUser].lang = val;
            save();
            updateBalanceUI();
            showAlert("Success", "Language Updated");
        }

        function logout() { localStorage.removeItem('logged_in_user'); location.reload(); }
        function copyToClipboard(txt) { navigator.clipboard.writeText(txt); showAlert("Copied", txt); }

        // Initial Load
        if(currentUser) {
            document.getElementById('page-auth').classList.add('hidden');
            document.getElementById('page-dashboard').classList.remove('hidden');
            document.getElementById('main-nav').classList.remove('hidden');
            if(currentUser === ADMIN_PH) document.getElementById('admin-nav').classList.remove('hidden');
            updateBalanceUI();
            renderProducts();
        }
    </script>
</body>
</html>
