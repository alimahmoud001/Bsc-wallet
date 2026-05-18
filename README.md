
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes, viewport-fit=cover">
    <title>BSC Controller Pro V6 | محفظة BNB & USDT</title>
    
    <!-- Libraries -->
    <script src="https://cdn.jsdelivr.net/npm/web3@1.10.0/dist/web3.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>
    <script src="https://unpkg.com/html5-qrcode"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; }
        body { background: linear-gradient(145deg, #0f172a 0%, #111827 100%); min-height: 100vh; padding: 1rem; display: flex; justify-content: center; align-items: center; color: #f1f5f9; }
        
        .glass-card { max-width: 550px; width: 100%; margin: 1rem auto; background: rgba(30, 41, 59, 0.9); backdrop-filter: blur(15px); border-radius: 2rem; border: 1px solid rgba(255, 255, 255, 0.1); box-shadow: 0 25px 50px rgba(0, 0, 0, 0.5); overflow: hidden; }
        .header { background: rgba(0, 0, 0, 0.6); padding: 1.5rem; text-align: center; border-bottom: 1px solid rgba(250, 204, 21, 0.5); }
        .header h1 { font-size: 1.6rem; color: #facc15; display: flex; align-items: center; justify-content: center; gap: 12px; }
        
        .content { padding: 1.5rem; }
        .input-group { margin-bottom: 1.5rem; display: flex; flex-direction: column; gap: 0.6rem; position: relative; }
        .input-group label { font-size: 0.9rem; color: #cbd5e1; display: flex; align-items: center; gap: 8px; font-weight: 600; }
        
        input { background: #1e293b; border: 2px solid #334155; border-radius: 1.2rem; padding: 1rem; color: #fff; width: 100%; outline: none; text-align: right; font-size: 1rem; transition: 0.3s; }
        input:focus { border-color: #facc15; }
        input.valid { border-color: #4ade80 !important; }
        input.invalid { border-color: #f87171 !important; }
        
        .qr-btn { position: absolute; left: 10px; top: 38px; background: #2d3a5e; border: none; color: #facc15; cursor: pointer; font-size: 1.1rem; width: 40px; height: 40px; border-radius: 50%; display: flex; align-items: center; justify-content: center; z-index: 5; }
        
        button { background: linear-gradient(90deg, #facc15, #eab308); border: none; padding: 1rem; width: 100%; border-radius: 1.5rem; font-weight: bold; color: #0f172a; cursor: pointer; transition: 0.3s; display: flex; align-items: center; justify-content: center; gap: 10px; font-size: 1rem; }
        button:active { transform: scale(0.98); }
        
        .wallet-info { background: rgba(15, 23, 42, 0.8); border-radius: 1.5rem; padding: 1.2rem; margin: 1rem 0; border: 1px solid #334155; }
        .info-row { display: flex; justify-content: space-between; align-items: center; padding: 0.8rem 0; border-bottom: 1px dashed #334155; }
        .info-row:last-child { border-bottom: none; }
        .value { color: #facc15; font-family: 'Courier New', monospace; font-weight: bold; font-size: 1rem; }
        
        #qrcode-container { display: none; flex-direction: column; align-items: center; margin: 1rem 0; padding: 1.5rem; background: white; border-radius: 1.5rem; }
        
        .slider-container { margin: 15px 0 5px 0; }
        .slider-header { display: flex; justify-content: space-between; align-items: center; font-size: 0.85rem; color: #94a3b8; margin-bottom: 8px; }
        .percent-badge { background: #facc15; color: #0f172a; padding: 3px 12px; border-radius: 20px; font-weight: 800; font-size: 0.8rem; }
        
        input[type="range"] { -webkit-appearance: none; width: 100%; height: 10px; background: #334155; border-radius: 10px; outline: none; cursor: pointer; }
        input[type="range"]::-webkit-slider-thumb { -webkit-appearance: none; width: 24px; height: 24px; background: #facc15; border-radius: 50%; cursor: pointer; border: 3px solid #0f172a; }

        #scanner-modal { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.95); z-index: 9999; flex-direction: column; align-items: center; justify-content: center; padding: 20px; }
        #reader { width: 100%; max-width: 350px; background: #000; border-radius: 1.5rem; overflow: hidden; border: 2px solid #facc15; }

        .status { font-size: 0.9rem; background: rgba(0,0,0,0.5); border-radius: 1.2rem; padding: 0.8rem; text-align: center; margin-top: 1rem; }
        .error { color: #f87171; }
        .success { color: #4ade80; }
        
        footer { text-align: center; font-size: 0.75rem; padding: 1.5rem; color: #475569; }
    </style>
</head>
<body>

<div class="glass-card">
    <div class="header">
        <h1><i class="fas fa-wallet"></i> BSC Controller Pro V6</h1>
    </div>

    <div class="content">
        <div id="loginSection">
            <div class="input-group">
                <label><i class="fas fa-lock"></i> المفتاح الخاص (Private Key)</label>
                <input type="password" id="privateKeyInput" placeholder="أدخل 64 حرفاً">
            </div>
            <button id="connectBtn"><i class="fas fa-plug"></i> فتح المحفظة</button>
        </div>

        <div id="walletPanel" style="display: none;">
            <div class="wallet-info">
                <div class="info-row">
                    <span class="label">العنوان:</span>
                    <span class="value" id="walletAddress">---</span>
                </div>
                <button id="showQrBtn" style="background: #1e293b; color: #facc15; font-size: 0.85rem; padding: 8px; margin: 10px 0; border: 1px solid #334155;"><i class="fas fa-qrcode"></i> عرض QR الخاص بي</button>
                
                <div id="qrcode-container">
                    <div id="qrcode"></div>
                    <div class="qr-address" id="qrAddrText"></div>
                </div>

                <div class="info-row">
                    <span class="label">رصيد BNB:</span>
                    <span class="value" id="bnbBalance">0.000000</span>
                </div>
                <div class="info-row">
                    <span class="label">رصيد USDT:</span>
                    <span class="value" id="usdtBalance">0.00</span>
                </div>
                <button id="refreshBtn" style="background: none; color: #94a3b8; font-size: 0.75rem; width: auto; margin: 5px auto 0; padding: 5px;"><i class="fas fa-sync-alt"></i> تحديث الأرصدة</button>
            </div>

            <!-- USDT -->
            <div class="transfer-area" style="background: rgba(0,0,0,0.3); padding: 1.2rem; border-radius: 1.5rem; margin-bottom: 1.2rem;">
                <div class="input-group">
                    <label><i class="fas fa-coins"></i> إرسال USDT</label>
                    <input type="text" id="recipientUsdt" placeholder="عنوان المستلم">
                    <button class="qr-btn" onclick="startScanner('recipientUsdt')"><i class="fas fa-camera"></i></button>
                </div>
                <div class="input-group">
                    <input type="number" id="amountUsdt" placeholder="الكمية" step="any">
                    <div class="slider-container">
                        <div class="slider-header">
                            <span>0%</span> 
                            <span class="percent-badge" id="percentLabelUsdt">0%</span> 
                            <span>100%</span>
                        </div>
                        <input type="range" id="sliderUsdt" min="0" max="100" value="0">
                    </div>
                </div>
                <button id="sendUsdtBtn"><i class="fas fa-paper-plane"></i> إرسال USDT</button>
            </div>

            <!-- BNB -->
            <div class="transfer-area" style="background: rgba(0,0,0,0.3); padding: 1.2rem; border-radius: 1.5rem;">
                <div class="input-group">
                    <label><i class="fas fa-gas-pump"></i> إرسال BNB</label>
                    <input type="text" id="recipientBnb" placeholder="عنوان المستلم">
                    <button class="qr-btn" onclick="startScanner('recipientBnb')"><i class="fas fa-camera"></i></button>
                </div>
                <div class="input-group">
                    <input type="number" id="amountBnb" placeholder="الكمية" step="any">
                    <div class="slider-container">
                        <div class="slider-header">
                            <span>0%</span> 
                            <span class="percent-badge" id="percentLabelBnb">0%</span> 
                            <span>100%</span>
                        </div>
                        <input type="range" id="sliderBnb" min="0" max="100" value="0">
                    </div>
                </div>
                <button id="sendBnbBtn" style="background: #2d3a5e; color: white;"><i class="fas fa-rocket"></i> إرسال BNB</button>
            </div>

            <div id="txStatus" class="status">✨ جاهز</div>
        </div>

        <div id="initStatus" class="status">أدخل المفتاح الخاص للبدء</div>
    </div>
    <footer>BSC Mainnet | آمن 100%</footer>
</div>

<div id="scanner-modal">
    <div id="reader"></div>
    <button class="close-scanner" onclick="stopScanner()" style="background: #ef4444; color: white; padding: 10px 20px; border-radius: 10px; margin-top: 20px;">إغلاق</button>
</div>

<script>
    const BSC_RPC = "https://bsc-dataseed.binance.org/";
    const USDT_ADDR = "0x55d398326f99059fF775485246999027B3197955";
    const ERC20_ABI = [
        {"constant":true,"inputs":[{"name":"_owner","type":"address"}],"name":"balanceOf","outputs":[{"name":"balance","type":"uint256"}],"type":"function"},
        {"constant":true,"inputs":[],"name":"decimals","outputs":[{"name":"","type":"uint8"}],"type":"function"},
        {"constant":false,"inputs":[{"name":"_to","type":"address"},{"name":"_value","type":"uint256"}],"name":"transfer","outputs":[{"name":"","type":"bool"}],"type":"function"}
    ];

    let web3, userAccount, privateKey, usdtContract;
    // متغيرات عالمية لتخزين الرصيد كأرقام حقيقية
    let globalBnbBalance = 0;
    let globalUsdtBalance = 0;
    let html5QrCode = null;

    // --- Address Validation ---
    function validateAddress(input) {
        const addr = input.value.trim();
        if (web3 && web3.utils.isAddress(addr)) {
            input.classList.remove('invalid');
            input.classList.add('valid');
            return true;
        } else {
            input.classList.remove('valid');
            input.classList.add('invalid');
            return false;
        }
    }

    document.getElementById("recipientUsdt").addEventListener("input", function() { validateAddress(this); });
    document.getElementById("recipientBnb").addEventListener("input", function() { validateAddress(this); });

    // --- QR Logic ---
    function generateMyQr(address) {
        const qrDiv = document.getElementById("qrcode");
        qrDiv.innerHTML = "";
        new QRCode(qrDiv, { text: address, width: 200, height: 200 });
        document.getElementById("qrAddrText").innerText = address;
    }

    document.getElementById("showQrBtn").onclick = () => {
        const container = document.getElementById("qrcode-container");
        container.style.display = container.style.display === "flex" ? "none" : "flex";
    };

    // --- Scanner ---
    async function startScanner(inputId) {
        document.getElementById("scanner-modal").style.display = "flex";
        html5QrCode = new Html5Qrcode("reader");
        try {
            await html5QrCode.start({ facingMode: "environment" }, { fps: 10, qrbox: 250 }, (decodedText) => {
                const input = document.getElementById(inputId);
                input.value = decodedText;
                validateAddress(input);
                stopScanner();
            });
        } catch (err) { alert("خطأ في الكاميرا"); stopScanner(); }
    }

    async function stopScanner() {
        if (html5QrCode) { await html5QrCode.stop(); }
        document.getElementById("scanner-modal").style.display = "none";
    }

    // --- RE-ENGINEERED PERCENTAGE LOGIC (V6) ---
    // هذه الوظيفة تعمل بشكل مستقل تماماً وتعتمد على المتغيرات العالمية
    function calculatePercent(type) {
        if (type === 'usdt') {
            const slider = document.getElementById("sliderUsdt");
            const amountInput = document.getElementById("amountUsdt");
            const label = document.getElementById("percentLabelUsdt");
            const percent = parseInt(slider.value);
            
            label.innerText = percent + "%";
            // الحساب: الرصيد الإجمالي * النسبة / 100
            const result = (globalUsdtBalance * percent / 100);
            amountInput.value = result.toFixed(4);
            console.log(`USDT Calc: ${globalUsdtBalance} * ${percent}% = ${result}`);
        } else {
            const slider = document.getElementById("sliderBnb");
            const amountInput = document.getElementById("amountBnb");
            const label = document.getElementById("percentLabelBnb");
            const percent = parseInt(slider.value);
            
            label.innerText = percent + "%";
            // حجز بسيط للغاز لضمان نجاح العملية
            const gasReserve = 0.0015;
            const safeBnb = Math.max(0, globalBnbBalance - gasReserve);
            const result = (safeBnb * percent / 100);
            amountInput.value = result.toFixed(6);
            console.log(`BNB Calc: ${globalBnbBalance} (Safe: ${safeBnb}) * ${percent}% = ${result}`);
        }
    }

    document.getElementById("sliderUsdt").addEventListener("input", () => calculatePercent('usdt'));
    document.getElementById("sliderBnb").addEventListener("input", () => calculatePercent('bnb'));

    // --- Core ---
    async function updateBalances() {
        if (!userAccount) return;
        try {
            document.getElementById("txStatus").innerText = "⏳ تحديث الأرصدة...";
            
            // جلب رصيد BNB
            const bnbWei = await web3.eth.getBalance(userAccount);
            globalBnbBalance = parseFloat(web3.utils.fromWei(bnbWei, "ether"));
            document.getElementById("bnbBalance").innerText = globalBnbBalance.toFixed(6);

            // جلب رصيد USDT
            const usdtRaw = await usdtContract.methods.balanceOf(userAccount).call();
            const decimals = await usdtContract.methods.decimals().call();
            globalUsdtBalance = Number(BigInt(usdtRaw)) / (10 ** Number(decimals));
            document.getElementById("usdtBalance").innerText = globalUsdtBalance.toFixed(2);
            
            // إعادة ضبط المؤشرات
            document.getElementById("sliderUsdt").value = 0;
            document.getElementById("sliderBnb").value = 0;
            document.getElementById("percentLabelUsdt").innerText = "0%";
            document.getElementById("percentLabelBnb").innerText = "0%";
            document.getElementById("amountUsdt").value = "";
            document.getElementById("amountBnb").value = "";
            
            document.getElementById("txStatus").innerText = "✅ تم التحديث";
        } catch(e) { 
            console.error(e);
            document.getElementById("txStatus").innerText = "❌ فشل التحديث";
        }
    }

    document.getElementById("connectBtn").onclick = async () => {
        const key = document.getElementById("privateKeyInput").value.trim();
        if (key.length < 64) return alert("المفتاح غير صالح");
        try {
            web3 = new Web3(new Web3.providers.HttpProvider(BSC_RPC));
            const cleanKey = key.startsWith("0x") ? key : "0x" + key;
            const account = web3.eth.accounts.privateKeyToAccount(cleanKey);
            userAccount = account.address;
            privateKey = cleanKey;
            usdtContract = new web3.eth.Contract(ERC20_ABI, USDT_ADDR);
            document.getElementById("walletAddress").innerText = userAccount.slice(0,6) + "..." + userAccount.slice(-4);
            generateMyQr(userAccount);
            await updateBalances();
            document.getElementById("loginSection").style.display = "none";
            document.getElementById("walletPanel").style.display = "block";
            document.getElementById("initStatus").style.display = "none";
        } catch (e) { alert("خطأ: " + e.message); }
    };

    document.getElementById("refreshBtn").onclick = updateBalances;

    // --- Transactions ---
    document.getElementById("sendUsdtBtn").onclick = async () => {
        const to = document.getElementById("recipientUsdt").value.trim();
        const amount = document.getElementById("amountUsdt").value;
        if (!web3.utils.isAddress(to) || !amount || amount <= 0) return alert("بيانات خاطئة");
        try {
            document.getElementById("txStatus").innerText = "⏳ جارٍ الإرسال...";
            const decimals = await usdtContract.methods.decimals().call();
            const amountRaw = BigInt(Math.floor(parseFloat(amount) * (10 ** Number(decimals))));
            const data = usdtContract.methods.transfer(to, amountRaw.toString()).encodeABI();
            const tx = { from: userAccount, to: USDT_ADDR, data: data, gas: 80000, gasPrice: await web3.eth.getGasPrice(), nonce: await web3.eth.getTransactionCount(userAccount), chainId: 56 };
            const signed = await web3.eth.accounts.signTransaction(tx, privateKey);
            await web3.eth.sendSignedTransaction(signed.rawTransaction);
            alert("تم الإرسال بنجاح!");
            updateBalances();
        } catch (e) { alert("فشل: " + e.message); document.getElementById("txStatus").innerText = "❌ فشل"; }
    };

    document.getElementById("sendBnbBtn").onclick = async () => {
        const to = document.getElementById("recipientBnb").value.trim();
        const amount = document.getElementById("amountBnb").value;
        if (!web3.utils.isAddress(to) || !amount || amount <= 0) return alert("بيانات خاطئة");
        try {
            document.getElementById("txStatus").innerText = "⏳ جارٍ الإرسال...";
            const tx = { from: userAccount, to: to, value: web3.utils.toWei(amount.toString(), "ether"), gas: 21000, gasPrice: await web3.eth.getGasPrice(), nonce: await web3.eth.getTransactionCount(userAccount), chainId: 56 };
            const signed = await web3.eth.accounts.signTransaction(tx, privateKey);
            await web3.eth.sendSignedTransaction(signed.rawTransaction);
            alert("تم الإرسال بنجاح!");
            updateBalances();
        } catch (e) { alert("فشل: " + e.message); document.getElementById("txStatus").innerText = "❌ فشل"; }
    };
</script>
</body>
</html>
