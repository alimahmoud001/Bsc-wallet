
# Bsc-wallet

<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>محفظة BSC - Infura API</title>
    <script src="https://cdn.jsdelivr.net/npm/web3@1.10.0/dist/web3.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/qrcodejs@1.0.0/qrcode.min.js"></script>
    <style>
        body { font-family: 'Segoe UI', Arial, sans-serif; max-width: 700px; margin: 50px auto; padding: 20px; background: #f0f2f5; direction: rtl; }
        .container { background: white; border-radius: 20px; padding: 25px; box-shadow: 0 4px 12px rgba(0,0,0,0.1); }
        input, button, select { width: 100%; padding: 12px; margin: 8px 0 16px; border: 1px solid #ddd; border-radius: 10px; font-size: 16px; box-sizing: border-box; }
        button { background: #1e88e5; color: white; font-weight: bold; border: none; cursor: pointer; transition: 0.3s; }
        button:hover { background: #0b5e9e; }
        button:disabled { background: #999; cursor: not-allowed; }
        .balance { background: #e8f0fe; padding: 15px; border-radius: 15px; margin: 15px 0; text-align: center; }
        .qr { text-align: center; margin: 20px 0; }
        .warning { background: #fff3e0; border-right: 4px solid #ff9800; padding: 12px; margin: 15px 0; font-size: 14px; }
        .error { color: #d32f2f; font-size: 14px; padding: 8px; background: #ffebee; border-radius: 8px; }
        .success { color: #2e7d32; font-size: 14px; padding: 8px; background: #e8f5e9; border-radius: 8px; }
        .info { color: #1565c0; font-size: 13px; margin-top: 5px; }
        hr { margin: 20px 0; }
        .status-badge { font-size: 12px; padding: 4px 8px; border-radius: 12px; display: inline-block; margin-right: 10px; }
        .online { background: #4caf50; color: white; }
    </style>
</head>
<body>
<div class="container">
    <h2>🔐 محفظة BSC - Infura API <span class="status-badge online">متصل عبر Infura</span></h2>
    
    <div class="warning">
        ⚠️ أدخل مفتاحك الخاص (Private Key) – لا تشاركه أبداً. يتم التعامل محلياً في متصفحك فقط.<br>
        📌 المفتاح يجب أن يكون بصيغة Hex (64 حرفاً)<br>
        🔌 تم تكوين Infura API مسبقاً: <strong>482a7c1c7cc1...b0cd</strong>
    </div>

    <label>🔑 المفتاح الخاص:</label>
    <input type="password" id="privateKey" placeholder="أدخل المفتاح الخاص (64 حرف Hex)" autocomplete="off">
    <button id="connectBtn">📡 فتح المحفظة</button>

    <div id="walletInfo" style="display:none;">
        <div class="balance">
            <h3>💰 رصيد BNB:</h3>
            <h2 id="bnbBalance">0</h2>
            <h3>💵 رصيد USDT (BEP-20):</h3>
            <h2 id="usdtBalance">0</h2>
        </div>

        <label>📬 عنوان محفظتك:</label>
        <input type="text" id="address" readonly onclick="this.select(); navigator.clipboard?.writeText(this.value)">
        <div id="qrcode" class="qr"></div>

        <hr>
        <h3>📤 إرسال العملة</h3>
        <select id="tokenSelect">
            <option value="BNB">BNB</option>
            <option value="USDT">USDT (BEP-20)</option>
        </select>
        <label>👤 عنوان المستلم:</label>
        <input type="text" id="toAddress" placeholder="0x...">
        <label>💲 المبلغ:</label>
        <input type="number" id="amount" placeholder="0.0" step="any">
        <button id="sendBtn">🚀 إرسال</button>
        <div id="txStatus"></div>
    </div>
</div>

<script>
    let web3, userAccount, currentAddress;
    
    // 🔑 مفتاح Infura API الخاص بك
    const INFURA_API_KEY = "482a7c1c7cc14ec78699c3f1c231b0cd";
    const BSC_RPC_URL = `https://bsc-mainnet.infura.io/v3/${INFURA_API_KEY}`;
    
    // عنوان عقد USDT على BSC الرئيسية
    const USDT_CONTRACT = "0x55d398326f99059fF775485246999027B3197955";
    const USDT_ABI = [
        "function balanceOf(address owner) view returns (uint256)",
        "function decimals() view returns (uint8)",
        "function transfer(address to, uint256 amount) returns (bool)"
    ];
    
    // دالة التحقق من صحة المفتاح الخاص
    function isValidPrivateKey(key) {
        let cleanKey = key.replace(/^0x/i, '');
        return /^[0-9a-fA-F]{64}$/.test(cleanKey);
    }
    
    // دالة تهيئة Web3 والمحفظة
    async function initWallet(privateKeyInput) {
        let privateKey = privateKeyInput.trim();
        
        if (!privateKey) {
            throw new Error("الرجاء إدخال المفتاح الخاص");
        }
        
        // تنظيف المفتاح
        if (!privateKey.startsWith("0x")) {
            privateKey = "0x" + privateKey;
        }
        
        // التحقق من صحة الطول
        const cleanKey = privateKey.replace(/^0x/i, '');
        if (cleanKey.length !== 64) {
            throw new Error(`المفتاح الخاص غير صالح: يجب أن يكون 64 حرفاً Hex (الطول الحالي: ${cleanKey.length})`);
        }
        
        if (!/^[0-9a-fA-F]{64}$/.test(cleanKey)) {
            throw new Error("المفتاح الخاص يحتوي على أحرف غير مسموحة (يسمح فقط بـ 0-9 و a-f)");
        }
        
        // تهيئة Web3 مع رابط Infura
        web3 = new Web3(BSC_RPC_URL);
        
        // اختبار الاتصال بـ Infura
        try {
            const blockNumber = await web3.eth.getBlockNumber();
            console.log("✅ متصل بـ BSC عبر Infura، رقم البلوك:", blockNumber);
        } catch(e) {
            throw new Error("فشل الاتصال بـ Infura: " + e.message + " - تأكد من صحة المفتاح");
        }
        
        // استعادة الحساب من المفتاح الخاص
        userAccount = web3.eth.accounts.privateKeyToAccount(privateKey);
        currentAddress = userAccount.address;
        
        return userAccount;
    }
    
    // تحديث الأرصدة
    async function updateBalances() {
        if (!web3 || !currentAddress) return;
        
        try {
            // رصيد BNB
            const bnbWei = await web3.eth.getBalance(currentAddress);
            const bnb = web3.utils.fromWei(bnbWei, 'ether');
            document.getElementById("bnbBalance").innerText = parseFloat(bnb).toFixed(6);
            
            // رصيد USDT
            const contract = new web3.eth.Contract(USDT_ABI, USDT_CONTRACT);
            const decimals = await contract.methods.decimals().call();
            const rawBalance = await contract.methods.balanceOf(currentAddress).call();
            const usdtBalance = rawBalance / Math.pow(10, parseInt(decimals));
            document.getElementById("usdtBalance").innerText = usdtBalance.toFixed(2);
            
        } catch(e) {
            console.error("خطأ في تحديث الرصيد:", e);
            document.getElementById("bnbBalance").innerText = "خطأ";
            document.getElementById("usdtBalance").innerText = "خطأ";
        }
    }
    
    // الاتصال بالمحفظة
    document.getElementById("connectBtn").onclick = async () => {
        const privKeyInput = document.getElementById("privateKey");
        const statusDiv = document.getElementById("txStatus");
        
        if (statusDiv) statusDiv.innerHTML = "";
        
        try {
            document.getElementById("connectBtn").disabled = true;
            document.getElementById("connectBtn").innerText = "⏳ جاري الاتصال بـ Infura...";
            
            const account = await initWallet(privKeyInput.value);
            
            // عرض معلومات المحفظة
            document.getElementById("address").value = account.address;
            document.getElementById("walletInfo").style.display = "block";
            
            // إنشاء رمز QR
            const qrContainer = document.getElementById("qrcode");
            qrContainer.innerHTML = "";
            new QRCode(qrContainer, {
                text: account.address,
                width: 160,
                height: 160
            });
            
            // تحديث الأرصدة
            await updateBalances();
            
            // تحديث الأرصدة كل 15 ثانية
            if (window.balanceInterval) clearInterval(window.balanceInterval);
            window.balanceInterval = setInterval(updateBalances, 15000);
            
            document.getElementById("connectBtn").innerText = "✅ تم فتح المحفظة";
            
        } catch(e) {
            alert("❌ خطأ: " + e.message);
            document.getElementById("connectBtn").innerText = "📡 فتح المحفظة";
            document.getElementById("walletInfo").style.display = "none";
        } finally {
            document.getElementById("connectBtn").disabled = false;
        }
    };
    
    // إرسال العملة
    document.getElementById("sendBtn").onclick = async () => {
        const token = document.getElementById("tokenSelect").value;
        const to = document.getElementById("toAddress").value.trim();
        const amount = parseFloat(document.getElementById("amount").value);
        const statusDiv = document.getElementById("txStatus");
        
        if (!to) {
            statusDiv.innerHTML = "<div class='error'>⚠️ الرجاء إدخال عنوان المستلم</div>";
            return;
        }
        
        if (isNaN(amount) || amount <= 0) {
            statusDiv.innerHTML = "<div class='error'>⚠️ الرجاء إدخال مبلغ صحيح</div>";
            return;
        }
        
        if (!web3 || !currentAddress) {
            statusDiv.innerHTML = "<div class='error'>⚠️ الرجاء فتح المحفظة أولاً</div>";
            return;
        }
        
        if (!web3.utils.isAddress(to)) {
            statusDiv.innerHTML = "<div class='error'>⚠️ عنوان المستلم غير صالح</div>";
            return;
        }
        
        try {
            document.getElementById("sendBtn").disabled = true;
            document.getElementById("sendBtn").innerText = "⏳ جاري الإرسال...";
            statusDiv.innerHTML = "<div class='info'>⏳ جاري التوقيع والإرسال عبر Infura...</div>";
            
            const gasPrice = await web3.eth.getGasPrice();
            
            if (token === "BNB") {
                // إرسال BNB
                const valueWei = web3.utils.toWei(amount.toString(), 'ether');
                const balanceWei = await web3.eth.getBalance(currentAddress);
                
                if (BigInt(valueWei) > BigInt(balanceWei)) {
                    throw new Error("رصيد BNB غير كافٍ (بما في ذلك رسوم الشبكة)");
                }
                
                const tx = {
                    from: currentAddress,
                    to: to,
                    value: valueWei,
                    gas: 21000,
                    gasPrice: gasPrice
                };
                
                const signedTx = await userAccount.signTransaction(tx);
                const receipt = await web3.eth.sendSignedTransaction(signedTx.rawTransaction);
                statusDiv.innerHTML = `<div class='success'>✅ تم إرسال ${amount} BNB بنجاح!<br>📝 هاش المعاملة: ${receipt.transactionHash.slice(0,20)}...<br>🔗 <a href="https://bscscan.com/tx/${receipt.transactionHash}" target="_blank">عرض على BscScan</a></div>`;
                
            } else if (token === "USDT") {
                // إرسال USDT
                const contract = new web3.eth.Contract(USDT_ABI, USDT_CONTRACT);
                const decimals = await contract.methods.decimals().call();
                const amountWithDecimals = BigInt(Math.floor(amount * Math.pow(10, parseInt(decimals))));
                
                // التحقق من الرصيد
                const balanceRaw = await contract.methods.balanceOf(currentAddress).call();
                if (amountWithDecimals > BigInt(balanceRaw)) {
                    throw new Error("رصيد USDT غير كافٍ");
                }
                
                const data = contract.methods.transfer(to, amountWithDecimals).encodeABI();
                
                const tx = {
                    from: currentAddress,
                    to: USDT_CONTRACT,
                    data: data,
                    gas: 65000,
                    gasPrice: gasPrice
                };
                
                const signedTx = await userAccount.signTransaction(tx);
                const receipt = await web3.eth.sendSignedTransaction(signedTx.rawTransaction);
                statusDiv.innerHTML = `<div class='success'>✅ تم إرسال ${amount} USDT بنجاح!<br>📝 هاش المعاملة: ${receipt.transactionHash.slice(0,20)}...<br>🔗 <a href="https://bscscan.com/tx/${receipt.transactionHash}" target="_blank">عرض على BscScan</a></div>`;
            }
            
            // تحديث الأرصدة بعد الإرسال
            setTimeout(() => updateBalances(), 5000);
            
        } catch(e) {
            console.error("خطأ في الإرسال:", e);
            let errorMsg = e.message;
            if (errorMsg.includes("insufficient funds")) errorMsg = "الرصيد غير كافٍ للمعاملة (بما في ذلك رسوم الشبكة)";
            if (errorMsg.includes("revert")) errorMsg = "فشلت المعاملة - قد يكون الرصيد غير كافٍ أو العنوان غير صحيح";
            statusDiv.innerHTML = `<div class='error'>❌ فشل الإرسال: ${errorMsg.slice(0,150)}</div>`;
        } finally {
            document.getElementById("sendBtn").disabled = false;
            document.getElementById("sendBtn").innerText = "🚀 إرسال";
        }
    };
    
    // تنظيف المتغيرات عند إغلاق الصفحة
    window.addEventListener("beforeunload", () => {
        if (window.balanceInterval) clearInterval(window.balanceInterval);
    });
    
    // عرض معلومات الاتصال عند تحميل الصفحة
    console.log("🔌 تم تكوين Infura API:", BSC_RPC_URL.replace(INFURA_API_KEY, "***HIDDEN***"));
</script>
</body>
</html>
