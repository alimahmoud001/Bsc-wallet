
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes, viewport-fit=cover">
    <title>BSC Controller Pro V4 | محفظة BNB & USDT</title>
    
    <!-- Libraries -->
    <script src="https://cdn.jsdelivr.net/npm/web3@1.10.0/dist/web3.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/qrcodejs/1.0.0/qrcode.min.js"></script>
    <script src="https://unpkg.com/html5-qrcode"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    
    <style>
        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif; }
        body { background: linear-gradient(145deg, #0f172a 0%, #111827 100%); min-height: 100vh; padding: 1rem; display: flex; justify-content: center; align-items: center; color: #f1f5f9; }
        
        .glass-card { max-width: 550px; width: 100%; margin: 1rem auto; background: rgba(30, 41, 59, 0.8); backdrop-filter: blur(15px); border-radius: 2rem; border: 1px solid rgba(255, 255, 255, 0.1); box-shadow: 0 25px 50px rgba(0, 0, 0, 0.4); overflow: hidden; }
        .header { background: rgba(0, 0, 0, 0.5); padding: 1.5rem; text-align: center; border-bottom: 1px solid rgba(250, 204, 21, 0.4); }
        .header h1 { font-size: 1.6rem; color: #facc15; display: flex; align-items: center; justify-content: center; gap: 12px; }
        
        .content { padding: 1.5rem; }
        .input-group { margin-bottom: 1.5rem; display: flex; flex-direction: column; gap: 0.6rem; position: relative; }
        .input-group label { font-size: 0.9rem; color: #cbd5e1; display: flex; align-items: center; gap: 8px; font-weight: 600; }
        
        input { background: #1e293b; border: 1px solid #334155; border-radius: 1.2rem; padding: 1rem; color: #fff; width: 100%; outline: none; text-align: right; font-size: 1rem; transition: 0.3s; }
        input:focus { border-color: #facc15; box-shadow: 0 0 0 2px rgba(250, 204, 21, 0.2); }
        
        .qr-btn { position: absolute; left: 10px; top: 38px; background: #2d3a5e; border: none; color: #facc15; cursor: pointer; font-size: 1.1rem; width: 40px; height: 40px; border-radius: 50%; display: flex; align-items: center; justify-content: center; transition: 0.2s; z-index: 5; }
        .qr-btn:hover { background: #3d4a6e; transform: scale(1.1); }
        
        button { background: linear-gradient(90deg, #facc15, #eab308); border: none; padding: 1rem; width: 100%; border-radius: 1.5rem; font-weight: bold; color: #0f172a; cursor: pointer; transition: 0.3s; display: flex; align-items: center; justify-content: center; gap: 10px; font-size: 1rem; }
        button:hover { opacity: 0.9; transform: translateY(-2px); }
        button:active { transform: scale(0.98); }
        
        .wallet-info { background: rgba(15, 23, 42, 0.7); border-radius: 1.5rem; padding: 1.2rem; margin: 1rem 0; border: 1px solid #334155; }
        .info-row { display: flex; justify-content: space-between; align-items: center; padding: 0.8rem 0; border-bottom: 1px dashed #334155; }
        .info-row:last-child { border-bottom: none; }
        .label { color: #94a3b8; font-size: 0.85rem; }
        .value { color: #facc15; font-family: 'Courier New', monospace; font-weight: bold; font-size: 1rem; }
        
        #qrcode-container { display: none; flex-direction: column; align-items: center; margin: 1rem 0; padding: 1.5rem; background: white; border-radius: 1.5rem; animation: fadeIn 0.3s ease; }
        #qrcode img { margin-bottom: 12px; border: 4px solid #fff; }
        .qr-address { color: #1e293b; font-size: 0.75rem; word-break: break-all; text-align: center; font-weight: bold; }

        .slider-container { margin: 15px 0 5px 0; padding: 0 5px; }
        .slider-header { display: flex; justify-content: space-between; align-items: center; font-size: 0.85rem; color: #94a3b8; margin-bottom: 8px; }
        .percent-badge { background: #facc15; color: #0f172a; padding: 3px 12px; border-radius: 20px; font-weight: 800; font-size: 0.8rem; box-shadow: 0 2px 5px rgba(0,0,0,0.2); }
        
        input[type="range"] { -webkit-appearance: none; width: 100%; height: 8px; background: #334155; border-radius: 10px; outline: none; cursor: pointer; }
        input[type="range"]::-webkit-slider-thumb { -webkit-appearance: none; width: 22px; height: 22px; background: #facc15; border-radius: 50%; cursor: pointer; border: 3px solid #0f172a; box-shadow: 0 0 10px rgba(250, 204, 21, 0.4); }

        #scanner-modal { display: none; position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.95); z-index: 9999; flex-direction: column; align-items: center; justify-content: center; padding: 20px; }
        #reader { width: 100%; max-width: 350px; background: #000; border-radius: 1.5rem; overflow: hidden; border: 2px solid #facc15; }
        .close-scanner { margin-top: 25px; background: #ef4444; color: white; width: auto; padding: 12px 30px; font-size: 1rem; }

        .status { font-size: 0.9rem; background: rgba(0,0,0,0.4); border-radius: 1.2rem; padding: 0.8rem; text-align: center; margin-top: 1rem; border: 1px solid transparent; }
        .error { color: #f87171; border-color: rgba(248, 113, 113, 0.3); }
        .success { color: #4ade80; border-color: rgba(74, 222, 128, 0.3); }
        .warning { color: #facc15; border-color: rgba(250, 204, 21, 0.3); }
        
        @keyframes fadeIn { from { opacity: 0; transform: translateY(-10px); } to { opacity: 1; transform: translateY(0); } }
        footer { text-align: center; font-size: 0.75rem; padding: 1.5rem; color: #475569; border-top: 1px solid rgba(255,255,255,0.05); }
    </style>
</head>
<body>

<div class="glass-card">
    <div class="header">
        <h1><i class="fas fa-wallet"></i> BSC Controller Pro V4</h1>
    </div>

    <div class="content">
        <!-- Login -->
        <div id="loginSection">
            <div class="input-group">
                <label><i class="fas fa-lock"></i> المفتاح الخاص (Private Key)</label>
                <input type="password" id="privateKeyInput" placeholder="أدخل 64 حرفاً سداسي عشري">
            </div>
            <button id="connectBtn"><i class="fas fa-plug"></i> فتح المحفظة والتحقق</button>
        </div>

        <!-- Wallet Panel -->
        <div id="walletPanel" style="display: none;">
            <div class="wallet-info">
                <div class="info-row">
                    <span class="label">عنوان المحفظة:</span>
                    <span class="value" id="walletAddress">---</span>
                </div>
                <button id="showQrBtn" style="background: #1e293b; color: #facc15; font-size: 0.85rem; padding: 8px; margin: 10px 0; border: 1px solid #334155;"><i class="fas fa-qrcode"></i> عرض كود QR الخاص بي</button>
                
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

            <!-- USDT Transfer -->
            <div class="transfer-area" style="background: rgba(0,0,0,0.25); padding: 1.2rem; border-radius: 1.5rem; margin-bottom: 1.2rem; border: 1px solid rgba(255,255,255,0.05);">
                <div class="input-group">
                    <label><i class="fas fa-coins"></i> إرسال USDT (BEP-20)</label>
                    <input type="text" id="recipientUsdt" placeholder="0x... عنوان المستلم">
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
                <button id="sendUsdtBtn"><i class="fas fa-paper-plane"></i> إرسال USDT الآن</button>
            </div>

            <!-- BNB Transfer -->
            <div class="transfer-area" style="background: rgba(0,0,0,0.25); padding: 1.2rem; border-radius: 1.5rem; border: 1px solid rgba(255,255,255,0.05);">
                <div class="input-group">
                    <label><i class="fas fa-gas-pump"></i> إرسال BNB (العملة الأساسية)</label>
                    <input type="text" id="recipientBnb" placeholder="0x... عنوان المستلم">
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
                <button id="sendBnbBtn" style="background: #2d3a5e; color: white;"><i class="fas fa-rocket"></i> إرسال BNB الآن</button>
            </div>

            <div id="txStatus" class="status">✨ جاهز لتنفيذ العمليات</div>
        </div>

        <div id="initStatus" class="status">يرجى إدخال المفتاح الخاص للوصول للمحفظة</div>
    </div>
    <footer>يدعم شبكة BSC Mainnet فقط | آمن 100%</footer>
</div>

<!-- Scanner Modal -->
<div id="scanner-modal">
    <div id="reader"></div>
    <div style="color: #facc15; margin-top: 15px; font-size: 0.8rem; text-align: center;">
        تأكد من السماح بالوصول للكاميرا واستخدام رابط HTTPS
    </div>
    <button class="close-scanner" onclick="stopScanner()">إلغاء المسح</button>
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
    let currentBnbBal = 0, currentUsdtBal = 0;
    let html5QrCode = null;

    // --- QR Logic ---
    function generateMyQr(address) {
        const qrDiv = document.getElementById("qrcode");
        qrDiv.innerHTML = "";
        new QRCode(qrDiv, { text: address, width: 200, height: 200, colorDark: "#000000", colorLight: "#ffffff" });
        document.getElementById("qrAddrText").innerText = address;
    }

    document.getElementById("showQrBtn").onclick = () => {
        const container = document.getElementById("qrcode-container");
        container.style.display = container.style.display === "flex" ? "none" : "flex";
    };

    // --- Scanner Logic (Improved) ---
    async function startScanner(inputId) {
        const modal = document.getElementById("scanner-modal");
        modal.style.display = "flex";
        
        if (!html5QrCode) {
            html5QrCode = new Html5Qrcode("reader");
        }
        
        const config = { fps: 15, qrbox: { width: 250, height: 250 }, aspectRatio: 1.0 };
        
        try {
            // محاولة فتح الكاميرا الخلفية أولاً
            await html5QrCode.start(
                { facingMode: "environment" }, 
                config, 
                (decodedText) => {
                    document.getElementById(inputId).value = decodedText;
                    stopScanner();
                }
            );
        } catch (err) {
            console.error("Camera Error:", err);
            alert("خطأ في الكاميرا: تأكد من استخدام متصفح حديث، رابط HTTPS، وإعطاء إذن الكاميرا.");
            stopScanner();
        }
    }

    async function stopScanner() {
        if (html5QrCode && html5QrCode.isScanning) {
            try {
                await html5QrCode.stop();
            } catch(e) { console.log("Stop error", e); }
        }
        document.getElementById("scanner-modal").style.display = "none";
    }

    // --- Percentage Calculation Logic (Fixed & Precise) ---
    const sliderUsdt = document.getElementById("sliderUsdt");
    const amountUsdtInput = document.getElementById("amountUsdt");
    const percentLabelUsdt = document.getElementById("percentLabelUsdt");

    sliderUsdt.addEventListener("input", function() {
        const percent = parseInt(this.value);
        percentLabelUsdt.innerText = percent + "%";
        if (currentUsdtBal > 0) {
            // حساب دقيق جداً
            const calculated = (currentUsdtBal * percent / 100);
            // عرض 4 أرقام عشرية كحد أقصى
            amountUsdtInput.value = Math.floor(calculated * 10000) / 10000;
        } else {
            amountUsdtInput.value = 0;
        }
    });

    const sliderBnb = document.getElementById("sliderBnb");
    const amountBnbInput = document.getElementById("amountBnb");
    const percentLabelBnb = document.getElementById("percentLabelBnb");

    sliderBnb.addEventListener("input", function() {
        const percent = parseInt(this.value);
        percentLabelBnb.innerText = percent + "%";
        if (currentBnbBal > 0) {
            // نترك 0.0015 BNB للغاز لضمان نجاح العملية عند اختيار 100%
            const gasReserve = 0.0015;
            const safeBal = Math.max(0, currentBnbBal - gasReserve);
            const calculated = (safeBal * percent / 100);
            amountBnbInput.value = Math.floor(calculated * 1000000) / 1000000;
        } else {
            amountBnbInput.value = 0;
        }
    });

    // --- Core Functions ---
    function setStatus(msg, type = 'success') {
        const el = document.getElementById("txStatus");
        el.innerText = msg;
        el.className = "status " + type;
    }

    async function updateBalances() {
        if (!userAccount) return;
        try {
            setStatus("⏳ جارٍ تحديث الأرصدة...", "warning");
            const bnbWei = await web3.eth.getBalance(userAccount);
            currentBnbBal = parseFloat(web3.utils.fromWei(bnbWei, "ether"));
            document.getElementById("bnbBalance").innerText = currentBnbBal.toFixed(6);

            const usdtRaw = await usdtContract.methods.balanceOf(userAccount).call();
            const decimals = await usdtContract.methods.decimals().call();
            currentUsdtBal = Number(BigInt(usdtRaw)) / (10 ** Number(decimals));
            document.getElementById("usdtBalance").innerText = currentUsdtBal.toFixed(2);
            
            // إعادة ضبط المؤشرات عند التحديث
            sliderUsdt.value = 0;
            sliderBnb.value = 0;
            percentLabelUsdt.innerText = "0%";
            percentLabelBnb.innerText = "0%";
            
            setStatus("✅ تم تحديث الأرصدة بنجاح", "success");
        } catch(e) { 
            console.error(e);
            setStatus("❌ فشل تحديث الأرصدة", "error");
        }
    }

    document.getElementById("refreshBtn").onclick = updateBalances;

    document.getElementById("connectBtn").onclick = async () => {
        const key = document.getElementById("privateKeyInput").value.trim();
        if (key.length < 64) return alert("المفتاح الخاص يجب أن يكون 64 حرفاً سداسي عشري.");
        
        try {
            setStatus("⏳ جارٍ الاتصال بالشبكة...", "warning");
            web3 = new Web3(new Web3.providers.HttpProvider(BSC_RPC));
            const cleanKey = key.startsWith("0x") ? key : "0x" + key;
            const account = web3.eth.accounts.privateKeyToAccount(cleanKey);
            
            userAccount = account.address;
            privateKey = cleanKey;
            usdtContract = new web3.eth.Contract(ERC20_ABI, USDT_ADDR);
            
            document.getElementById("walletAddress").innerText = userAccount;
            generateMyQr(userAccount);
            await updateBalances();
            
            document.getElementById("loginSection").style.display = "none";
            document.getElementById("walletPanel").style.display = "block";
            document.getElementById("initStatus").style.display = "none";
        } catch (e) {
            alert("فشل الاتصال: " + e.message);
            setStatus("❌ خطأ في المفتاح أو الشبكة", "error");
        }
    };

    // --- Transactions ---
    document.getElementById("sendUsdtBtn").onclick = async () => {
        const to = document.getElementById("recipientUsdt").value.trim();
        const amount = amountUsdtInput.value;
        if (!web3.utils.isAddress(to) || !amount || amount <= 0) return setStatus("⚠️ يرجى إدخال عنوان صحيح وكمية صالحة", "error");

        try {
            setStatus("⏳ جارٍ معالجة معاملة USDT...", "warning");
            const decimals = await usdtContract.methods.decimals().call();
            const amountRaw = BigInt(Math.floor(parseFloat(amount) * (10 ** Number(decimals))));
            const data = usdtContract.methods.transfer(to, amountRaw.toString()).encodeABI();
            
            const tx = {
                from: userAccount, to: USDT_ADDR, data: data,
                gas: 85000, gasPrice: await web3.eth.getGasPrice(),
                nonce: await web3.eth.getTransactionCount(userAccount), chainId: 56
            };
            
            const signed = await web3.eth.accounts.signTransaction(tx, privateKey);
            const receipt = await web3.eth.sendSignedTransaction(signed.rawTransaction);
            setStatus(`✅ نجاح! Hash: ${receipt.transactionHash.slice(0,15)}...`, "success");
            await updateBalances();
        } catch (e) { setStatus("❌ فشل الإرسال: " + e.message, "error"); }
    };

    document.getElementById("sendBnbBtn").onclick = async () => {
        const to = document.getElementById("recipientBnb").value.trim();
        const amount = amountBnbInput.value;
        if (!web3.utils.isAddress(to) || !amount || amount <= 0) return setStatus("⚠️ يرجى إدخال عنوان صحيح وكمية صالحة", "error");

        try {
            setStatus("⏳ جارٍ معالجة معاملة BNB...", "warning");
            const tx = {
                from: userAccount, to: to, value: web3.utils.toWei(amount.toString(), "ether"),
                gas: 21000, gasPrice: await web3.eth.getGasPrice(),
                nonce: await web3.eth.getTransactionCount(userAccount), chainId: 56
            };
            const signed = await web3.eth.accounts.signTransaction(tx, privateKey);
            const receipt = await web3.eth.sendSignedTransaction(signed.rawTransaction);
            setStatus(`✅ نجاح! Hash: ${receipt.transactionHash.slice(0,15)}...`, "success");
            await updateBalances();
        } catch (e) { setStatus("❌ فشل الإرسال: " + e.message, "error"); }
    };
</script>
</
