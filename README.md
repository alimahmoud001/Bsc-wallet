<html lang="ar">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes, viewport-fit=cover">
    <title>BSC Controller | محفظة BNB & USDT</title>
    <!-- Web3.js Library -->
    <script src="https://cdn.jsdelivr.net/npm/web3@1.10.0/dist/web3.min.js"></script>
    <!-- Font Awesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css">
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }

        body {
            background: linear-gradient(145deg, #0f172a 0%, #111827 100%);
            min-height: 100vh;
            padding: 1rem;
            display: flex;
            justify-content: center;
            align-items: center;
        }

        /* Main container - responsive card */
        .glass-card {
            max-width: 550px;
            width: 100%;
            margin: 1rem auto;
            background: rgba(30, 41, 59, 0.75);
            backdrop-filter: blur(14px);
            border-radius: 2rem;
            border: 1px solid rgba(255, 255, 255, 0.2);
            box-shadow: 0 25px 45px rgba(0, 0, 0, 0.3), 0 0 0 1px rgba(255, 255, 255, 0.05);
            overflow: hidden;
            transition: all 0.3s ease;
        }

        .header {
            background: rgba(0, 0, 0, 0.4);
            padding: 1.2rem 1.5rem;
            text-align: center;
            border-bottom: 1px solid rgba(255, 215, 0, 0.3);
        }

        .header h1 {
            font-size: 1.7rem;
            color: #facc15;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
            flex-wrap: wrap;
        }

        .header h1 i {
            font-size: 1.8rem;
            text-shadow: 0 0 5px #fbbf24;
        }

        .sub {
            color: #94a3b8;
            font-size: 0.8rem;
            margin-top: 6px;
        }

        .content {
            padding: 1.6rem;
        }

        /* Input group style */
        .input-group {
            margin-bottom: 1.5rem;
            display: flex;
            flex-direction: column;
            gap: 0.5rem;
        }

        .input-group label {
            font-size: 0.85rem;
            font-weight: 600;
            color: #cbd5e1;
            letter-spacing: 0.5px;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .input-group label i {
            width: 1.4rem;
            color: #facc15;
        }

        input, textarea {
            background: #1e293b;
            border: 1px solid #334155;
            border-radius: 1.2rem;
            padding: 0.9rem 1.2rem;
            color: #f1f5f9;
            font-size: 0.9rem;
            outline: none;
            transition: all 0.2s;
            resize: vertical;
        }

        input:focus, textarea:focus {
            border-color: #facc15;
            box-shadow: 0 0 0 2px rgba(250, 204, 21, 0.3);
        }

        .helper {
            font-size: 0.7rem;
            color: #64748b;
            margin-top: 4px;
        }

        button {
            background: linear-gradient(90deg, #facc15, #eab308);
            border: none;
            padding: 0.9rem;
            width: 100%;
            border-radius: 2rem;
            font-weight: bold;
            font-size: 1rem;
            color: #0f172a;
            cursor: pointer;
            transition: 0.2s;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 10px;
            margin-top: 0.5rem;
            margin-bottom: 1.2rem;
            box-shadow: 0 4px 10px rgba(0,0,0,0.2);
        }

        button:active {
            transform: scale(0.97);
        }

        .wallet-info {
            background: #0f172a80;
            border-radius: 1.5rem;
            padding: 1rem;
            margin: 1rem 0;
            border: 1px solid #334155;
        }

        .info-row {
            display: flex;
            justify-content: space-between;
            align-items: center;
            flex-wrap: wrap;
            padding: 0.7rem 0;
            border-bottom: 1px dashed #334155;
        }

        .info-row:last-child {
            border-bottom: none;
        }

        .label {
            color: #94a3b8;
            font-size: 0.8rem;
        }

        .value {
            color: #facc15;
            font-weight: bold;
            word-break: break-all;
            text-align: right;
            max-width: 65%;
            font-family: monospace;
        }

        .action-buttons {
            display: flex;
            flex-direction: column;
            gap: 12px;
            margin-top: 1rem;
        }

        .small-btn {
            background: #2d3a5e;
            color: white;
            padding: 0.7rem;
            font-size: 0.85rem;
            margin-top: 0;
            box-shadow: none;
        }

        .small-btn i {
            color: #facc15;
        }

        .transfer-area {
            background: #0b1120;
            border-radius: 1.2rem;
            padding: 1rem;
            margin-top: 1rem;
        }

        .status {
            font-size: 0.8rem;
            background: #00000055;
            border-radius: 1rem;
            padding: 0.6rem;
            text-align: center;
            color: #cbd5e6;
            margin-top: 0.8rem;
        }

        .error {
            color: #f87171;
        }

        .success {
            color: #4ade80;
        }

        .warning {
            color: #facc15;
        }

        footer {
            text-align: center;
            font-size: 0.7rem;
            padding: 1rem;
            color: #475569;
            border-top: 1px solid #1e293b;
        }

        @media (max-width: 480px) {
            .content {
                padding: 1rem;
            }
            .value {
                max-width: 55%;
                font-size: 0.75rem;
            }
            .header h1 {
                font-size: 1.3rem;
            }
        }
    </style>
</head>
<body>
<div class="glass-card">
    <div class="header">
        <h1><i class="fab fa-btc"></i> BSC Controller <i class="fab fa-ethereum"></i></h1>
        <div class="sub">شبكة BSC | BNB & USDT | مفتاح خاص فرعي فقط</div>
    </div>

    <div class="content">
        <!-- قسم إدخال المفتاح الخاص الفرعي (غير موسع) -->
        <div class="input-group">
            <label><i class="fas fa-key"></i> المفتاح الخاص الفرعي (Sub Private Key)</label>
            <input type="password" id="privateKeyInput" placeholder="0x... أو مفتاح خاص عادي (غير موسع)" autocomplete="off">
            <div class="helper">⚠️ مفتاح فرعي وليس مفتاح محفظة رئيسي / موسع. يدعم فقط المفاتيح الخاصة العادية (64 حرفًا سداسيًا)</div>
        </div>

        <button id="connectBtn"><i class="fas fa-unlock-alt"></i> فتح المحفظة & التحقق</button>

        <!-- قسم عرض بيانات المحفظة (يظهر بعد الاتصال) -->
        <div id="walletPanel" style="display: none;">
            <div class="wallet-info">
                <div class="info-row">
                    <span class="label"><i class="fas fa-wallet"></i> عنوان المحفظة:</span>
                    <span class="value" id="walletAddress">---</span>
                </div>
                <div class="info-row">
                    <span class="label"><i class="fas fa-coins"></i> رصيد BNB:</span>
                    <span class="value" id="bnbBalance">0 BNB</span>
                </div>
                <div class="info-row">
                    <span class="label"><i class="fas fa-dollar-sign"></i> رصيد USDT (BEP-20):</span>
                    <span class="value" id="usdtBalance">0 USDT</span>
                </div>
            </div>

            <div class="transfer-area">
                <div class="input-group">
                    <label><i class="fas fa-paper-plane"></i> تحويل USDT إلى عنوان</label>
                    <input type="text" id="recipientAddr" placeholder="0x... عنوان المستلم">
                    <input type="text" id="usdtAmount" placeholder="الكمية (USDT)" step="any">
                    <button id="sendUsdtBtn" class="small-btn"><i class="fas fa-exchange-alt"></i> إرسال USDT</button>
                </div>
                <div class="input-group">
                    <label><i class="fas fa-gas-pump"></i> تحويل BNB إلى عنوان</label>
                    <input type="text" id="recipientBnbAddr" placeholder="0x... عنوان المستلم">
                    <input type="text" id="bnbAmount" placeholder="الكمية (BNB)" step="any">
                    <button id="sendBnbBtn" class="small-btn"><i class="fas fa-rocket"></i> إرسال BNB</button>
                </div>
            </div>
            <div id="txStatus" class="status">✨ جاهز لتنفيذ المعاملات</div>
        </div>

        <div id="initStatus" class="status"><i class="fas fa-plug"></i> أدخل المفتاح الخاص الفرعي واضغط "فتح المحفظة"</div>
    </div>
    <footer>
        <i class="fas fa-shield-alt"></i> يدعم BSC Mainnet | USDT Contract: 0x55d398326f99059fF775485246999027B3197955
    </footer>
</div>

<script>
    // ------------------- التكوين الثابت -------------------
    // BSC Mainnet RPC (بديل موثوق)
    const BSC_RPC = "https://bsc-dataseed.binance.org/";
    // عنوان عقد USDT المعروف على BSC (BEP-20)
    const USDT_CONTRACT_ADDRESS = "0x55d398326f99059fF775485246999027B3197955";
    // ABI الأساسي لـ ERC20 (balanceOf, decimals, transfer)
    const ERC20_ABI = [
        "function balanceOf(address owner) view returns (uint256)",
        "function decimals() view returns (uint8)",
        "function transfer(address to, uint256 amount) returns (bool)",
        "function symbol() view returns (string)"
    ];
    
    // متغيرات الحالة
    let web3 = null;
    let userAccount = null;      // عنوان المستخدم المستخرج من المفتاح
    let privateKey = null;       // المفتاح الخاص المخزن (للتوقيع)
    let usdtContract = null;
    let isConnected = false;
    
    // توكن BSCScan API (المقدم من المستخدم - سيتم استخدامه لاحقاً في حال الحاجة لقراءة API لكننا نعتمد على RPC)
    const BSCSCAN_API_KEY = "ZM8ACMJB67C2IXKKBF8URFUNSY";
    console.log("BSCScan API Key loaded (اختياري للقراءة)", BSCSCAN_API_KEY);
    
    // Elements
    const connectBtn = document.getElementById("connectBtn");
    const privateKeyInput = document.getElementById("privateKeyInput");
    const walletPanel = document.getElementById("walletPanel");
    const initStatusDiv = document.getElementById("initStatus");
    const txStatusDiv = document.getElementById("txStatus");
    const walletAddressSpan = document.getElementById("walletAddress");
    const bnbBalanceSpan = document.getElementById("bnbBalance");
    const usdtBalanceSpan = document.getElementById("usdtBalance");
    
    // Helper function لعرض الحالة
    function setStatus(element, message, isError = false) {
        if (element) {
            element.innerHTML = message;
            element.classList.remove("error", "success", "warning");
            if (isError) element.classList.add("error");
            else if (message.includes("نجاح") || message.includes("تم")) element.classList.add("success");
            else if (message.includes("قيد")) element.classList.add("warning");
        }
    }
    
    // تهيئة web3 وإرجاع الكائن
    async function initWeb3() {
        if (web3) return web3;
        const provider = new Web3.providers.HttpProvider(BSC_RPC);
        web3 = new Web3(provider);
        return web3;
    }
    
    // التحقق من صحة المفتاح الخاص (فرعي - طول 64 حرف عادي بدون 0x أو مع 0x)
    function isValidPrivateKey(key) {
        if (!key) return false;
        let cleanKey = key.trim();
        if (cleanKey.startsWith("0x")) cleanKey = cleanKey.slice(2);
        // المفتاح الخاص العادي يكون 64 حرف سداسي عشري (غير موسع)
        return /^[a-fA-F0-9]{64}$/.test(cleanKey);
    }
    
    // استخراج عنوان المحفظة من المفتاح الخاص
    async function getAddressFromPrivateKey(pkey) {
        await initWeb3();
        let cleanKey = pkey.trim();
        if (cleanKey.startsWith("0x")) cleanKey = cleanKey.slice(2);
        const account = web3.eth.accounts.privateKeyToAccount("0x" + cleanKey);
        return account.address;
    }
    
    // جلب رصيد BNB بالـ ether
    async function fetchBnbBalance(address) {
        try {
            const balanceWei = await web3.eth.getBalance(address);
            const balanceBnb = web3.utils.fromWei(balanceWei, "ether");
            return parseFloat(balanceBnb).toFixed(6);
        } catch (e) {
            console.error("BNB balance error", e);
            return "0";
        }
    }
    
    // جلب رصيد USDT (يتم استدعاؤه بعد إنشاء العقد)
    async function fetchUsdtBalance(address) {
        if (!usdtContract) return "0";
        try {
            const rawBalance = await usdtContract.methods.balanceOf(address).call();
            const decimals = await usdtContract.methods.decimals().call();
            const divisor = Math.pow(10, decimals);
            const balanceFormatted = (rawBalance / divisor).toFixed(2);
            return balanceFormatted;
        } catch (e) {
            console.error("USDT balance error", e);
            return "0";
        }
    }
    
    // تحديث جميع الأرصدة وعرض العنوان
    async function updateWalletUI() {
        if (!userAccount || !web3) return;
        walletAddressSpan.innerText = userAccount;
        const bnbBal = await fetchBnbBalance(userAccount);
        bnbBalanceSpan.innerText = `${bnbBal} BNB`;
        if (usdtContract) {
            const usdtBal = await fetchUsdtBalance(userAccount);
            usdtBalanceSpan.innerText = `${usdtBal} USDT`;
        } else {
            usdtBalanceSpan.innerText = "— USDT";
        }
    }
    
    // إرسال BNB (معاملة عادية)
    async function sendBnb(toAddress, amountInBnb) {
        if (!web3 || !userAccount || !privateKey) throw new Error("المحفظة غير متصلة");
        if (!toAddress || !web3.utils.isAddress(toAddress)) throw new Error("عنوان المستلم غير صالح");
        const amountWei = web3.utils.toWei(amountInBnb.toString(), "ether");
        const balanceWei = await web3.eth.getBalance(userAccount);
        if (BigInt(balanceWei) < BigInt(amountWei)) throw new Error("رصيد BNB غير كافٍ + رسوم الشبكة");
        
        // تقدير الغاز
        const gasPrice = await web3.eth.getGasPrice();
        const txCount = await web3.eth.getTransactionCount(userAccount);
        const txObj = {
            from: userAccount,
            to: toAddress,
            value: amountWei,
            gas: 21000,
            gasPrice: gasPrice,
            nonce: txCount,
            chainId: 56  // BSC Mainnet
        };
        // توقيع المعاملة
        const signedTx = await web3.eth.accounts.signTransaction(txObj, privateKey);
        const receipt = await web3.eth.sendSignedTransaction(signedTx.rawTransaction);
        return receipt;
    }
    
    // إرسال USDT باستخدام عقد BEP20
    async function sendUsdt(toAddress, amountUsdt) {
        if (!web3 || !userAccount || !privateKey) throw new Error("المحفظة غير متصلة");
        if (!usdtContract) throw new Error("عقد USDT لم يتم تحميله");
        if (!toAddress || !web3.utils.isAddress(toAddress)) throw new Error("عنوان المستلم غير صالح");
        
        const decimals = await usdtContract.methods.decimals().call();
        const amountRaw = BigInt(Math.floor(parseFloat(amountUsdt) * (10 ** decimals)));
        if (amountRaw <= 0) throw new Error("الكمية غير صالحة");
        
        // التحقق من الرصيد
        const currentBalanceRaw = await usdtContract.methods.balanceOf(userAccount).call();
        if (BigInt(currentBalanceRaw) < amountRaw) throw new Error("رصيد USDT غير كافٍ");
        
        const gasPrice = await web3.eth.getGasPrice();
        const nonce = await web3.eth.getTransactionCount(userAccount);
        
        // بيانات transfer
        const transferData = usdtContract.methods.transfer(toAddress, amountRaw.toString()).encodeABI();
        const txObj = {
            from: userAccount,
            to: USDT_CONTRACT_ADDRESS,
            data: transferData,
            gas: 80000,   // تقدير آمن لإرسال USDT
            gasPrice: gasPrice,
            nonce: nonce,
            chainId: 56
        };
        const signedTx = await web3.eth.accounts.signTransaction(txObj, privateKey);
        const receipt = await web3.eth.sendSignedTransaction(signedTx.rawTransaction);
        return receipt;
    }
    
    // ربط وعرض المحفظة بناءً على المفتاح الخاص الفرعي
    async function connectWalletWithPrivateKey(rawKey) {
        try {
            setStatus(initStatusDiv, "⏳ جارٍ التحقق من المفتاح الخاص ...", false);
            if (!isValidPrivateKey(rawKey)) {
                throw new Error("المفتاح الخاص غير صالح: يجب أن يكون مفتاحًا فرعيًا (غير موسع) مكونًا من 64 حرفًا سداسيًا أو يبدأ بـ 0x");
            }
            // تهيئة web3
            await initWeb3();
            let cleanKey = rawKey.trim();
            if (cleanKey.startsWith("0x")) cleanKey = cleanKey.slice(2);
            const fullPrivate = "0x" + cleanKey;
            
            // استخراج العنوان
            const accountAddr = await getAddressFromPrivateKey(fullPrivate);
            userAccount = accountAddr;
            privateKey = fullPrivate;
            
            // إنشاء عقد USDT
            usdtContract = new web3.eth.Contract(ERC20_ABI, USDT_CONTRACT_ADDRESS);
            
            // تحديث الواجهة
            await updateWalletUI();
            walletPanel.style.display = "block";
            setStatus(initStatusDiv, `✅ متصل بنجاح: ${userAccount.slice(0,6)}...${userAccount.slice(-4)}`, false);
            setStatus(txStatusDiv, "✔️ المحفظة جاهزة | يمكنك إرسال BNB أو USDT", false);
            isConnected = true;
        } catch (err) {
            console.error(err);
            walletPanel.style.display = "none";
            setStatus(initStatusDiv, `❌ فشل الاتصال: ${err.message}`, true);
            isConnected = false;
            userAccount = null;
            privateKey = null;
        }
    }
    
    // ---- الأحداث ----
    connectBtn.addEventListener("click", async () => {
        const pkey = privateKeyInput.value.trim();
        if (!pkey) {
            setStatus(initStatusDiv, "⚠️ الرجاء إدخال المفتاح الخاص الفرعي", true);
            return;
        }
        await connectWalletWithPrivateKey(pkey);
    });
    
    // زر إرسال USDT
    const sendUsdtBtn = document.getElementById("sendUsdtBtn");
    sendUsdtBtn.addEventListener("click", async () => {
        if (!isConnected || !userAccount) {
            setStatus(txStatusDiv, "⚠️ يجب فتح المحفظة أولاً بمفتاح خاص صحيح", true);
            return;
        }
        const recipient = document.getElementById("recipientAddr").value.trim();
        const amount = document.getElementById("usdtAmount").value.trim();
        if (!recipient || !amount) {
            setStatus(txStatusDiv, "⚠️ أدخل عنوان المستلم وكمية USDT", true);
            return;
        }
        if (!web3.utils.isAddress(recipient)) {
            setStatus(txStatusDiv, "⚠️ عنوان المستلم غير صحيح", true);
            return;
        }
        const amountNum = parseFloat(amount);
        if (isNaN(amountNum) || amountNum <= 0) {
            setStatus(txStatusDiv, "⚠️ الكمية يجب أن تكون رقماً موجباً", true);
            return;
        }
        setStatus(txStatusDiv, "⏳ جارٍ إرسال USDT ... (قد يستغرق 15-30 ثانية)", false);
        try {
            const receipt = await sendUsdt(recipient, amountNum);
            setStatus(txStatusDiv, `✅ تم إرسال ${amountNum} USDT بنجاح! التجزئة: ${receipt.transactionHash.slice(0,10)}...`, false);
            // تحديث الرصيد بعد العملية
            await updateWalletUI();
            // تفريغ حقول المستلم والكمية اختياري
            document.getElementById("recipientAddr").value = "";
            document.getElementById("usdtAmount").value = "";
        } catch (err) {
            setStatus(txStatusDiv, `❌ فشل إرسال USDT: ${err.message}`, true);
        }
    });
    
    // زر إرسال BNB
    const sendBnbBtn = document.getElementById("sendBnbBtn");
    sendBnbBtn.addEventListener("click", async () => {
        if (!isConnected || !userAccount) {
            setStatus(txStatusDiv, "⚠️ يجب فتح المحفظة أولاً بمفتاح خاص صحيح", true);
            return;
        }
        const recipient = document.getElementById("recipientBnbAddr").value.trim();
        const amount = document.getElementById("bnbAmount").value.trim();
        if (!recipient || !amount) {
            setStatus(txStatusDiv, "⚠️ أدخل عنوان المستلم وكمية BNB", true);
            return;
        }
        if (!web3.utils.isAddress(recipient)) {
            setStatus(txStatusDiv, "⚠️ عنوان المستلم غير صحيح", true);
            return;
        }
        const amountNum = parseFloat(amount);
        if (isNaN(amountNum) || amountNum <= 0) {
            setStatus(txStatusDiv, "⚠️ الكمية يجب أن تكون رقماً موجباً", true);
            return;
        }
        setStatus(txStatusDiv, "⏳ جارٍ إرسال BNB ... (تحقق من الرصيد والغاز)", false);
        try {
            const receipt = await sendBnb(recipient, amountNum);
            setStatus(txStatusDiv, `✅ تم إرسال ${amountNum} BNB بنجاح! التجزئة: ${receipt.transactionHash.slice(0,10)}...`, false);
            await updateWalletUI();
            document.getElementById("recipientBnbAddr").value = "";
            document.getElementById("bnbAmount").value = "";
        } catch (err) {
            setStatus(txStatusDiv, `❌ فشل إرسال BNB: ${err.message}`, true);
        }
    });
    
    // تحسين تجربة المستخدم: إذا تم الاتصال مسبقاً لا حاجة لإعادة تحميل لكن يتم التحقق
    // في البداية نعرض أنه لم يتم الاتصال بعد
    (function preCheck() {
        // لا توجد محفظة في البداية
        setStatus(initStatusDiv, "🔐 أدخل المفتاح الخاص الفرعي (غير الموسع) واضغط فتح المحفظة", false);
    })();
    
    // مساعدة: إظهار تلميح لأداة BSCScan API (ليس إجبارياً لعمليات الإرسال)
    // API key يستخدم فقط في حال التوسع، لكن غير مطلوب للمعاملات. فقط للعلم
    console.log("API key available for potential future use:", BSCSCAN_API_KEY);
    
    // ملاحظة: نؤكد استخدام المفتاح الخاص الفرعي (sub private key) وليس موسع. هذا الكود لا يقبل mnemonics.
    // جميع المعاملات موقعة محلياً وتُرسل إلى RPC العام.
    
    // تصميم متجاوب بالكامل، يحافظ على الأمان عبر توقيع المعاملات محلياً.
</script>
</body>
</html>
