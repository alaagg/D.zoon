<!DOCTYPE html>
<html lang="ar">
<head>
    <meta charset="UTF-8">
    <title>واجهة التعدين الذكية</title>
    <style>
        body { font-family: Arial, sans-serif; background: #111; color: #eee; padding: 20px; }
        .field { margin-bottom: 10px; }
        label { display: block; margin-bottom: 5px; }
        input[type="text"] { width: 100%; padding: 5px; font-size: 14px; }
        button { padding: 10px 20px; margin-top: 10px; font-size: 16px; cursor: pointer; }
        .result { padding: 10px; margin-top: 10px; background: #222; border-radius: 5px; }
        .success { color: #0f0; font-weight: bold; }
        .fail { color: #f00; font-weight: bold; }
    </style>
</head>
<body>
    <h1>محاكاة التعدين الذكية</h1>

    <div class="field">
        <label>Version (hex):</label>
        <input type="text" id="version" value="20000000">
    </div>
    <div class="field">
        <label>Prev Hash (hex):</label>
        <input type="text" id="prevhash" value="0000000000000000000example0000000000000000000">
    </div>
    <div class="field">
        <label>Merkle Root (hex):</label>
        <input type="text" id="merkleroot" value="4dexample000000000000000000000000000000000000000">
    </div>
    <div class="field">
        <label>Time (hex):</label>
        <input type="text" id="time" value="65000000">
    </div>
    <div class="field">
        <label>Bits (hex):</label>
        <input type="text" id="bits" value="170c6a00">
    </div>
    <div class="field">
        <label>Nonce (32 بت):</label>
        <input type="text" id="nonce" value="00000000000000000000000000000000" maxlength="32">
    </div>
    <button id="start">ابدأ البحث التلقائي</button>

    <div class="result">
        <p>الهاش الناتج: <span id="hash">---</span></p>
        <p>الحالة: <span id="status">---</span></p>
    </div>

    <script>
        async function sha256(message) {
            const msgBuffer = new TextEncoder().encode(message);
            const hashBuffer = await crypto.subtle.digest('SHA-256', msgBuffer);
            const hashArray = Array.from(new Uint8Array(hashBuffer));
            return hashArray.map(b => b.toString(16).padStart(2, '0')).join('');
        }

        async function doubleSHA256(hex) {
            const first = await sha256(hex);
            return sha256(first);
        }

        function hexToTarget(bits) {
            const n = parseInt(bits, 16);
            const exp = n >> 24;
            const mant = n & 0xffffff;
            return mant * (2 ** (8 * (exp - 3)));
        }

        async function updateHash() {
            const version = document.getElementById('version').value;
            const prev = document.getElementById('prevhash').value;
            const merkle = document.getElementById('merkleroot').value;
            const time = document.getElementById('time').value;
            const bits = document.getElementById('bits').value;
            const nonce = document.getElementById('nonce').value;

            const header = version + prev + merkle + time + bits + nonce;
            const hash = await doubleSHA256(header);
            document.getElementById('hash').textContent = hash;

            const target = hexToTarget(bits).toString(16).padStart(64, '0');
            if (hash < target) {
                document.getElementById('status').textContent = 'ناجح ✅';
                document.getElementById('status').className = 'success';
            } else {
                document.getElementById('status').textContent = 'فشل ❌';
                document.getElementById('status').className = 'fail';
            }
            return hash;
        }

        async function autoSearch() {
            let nonce = document.getElementById('nonce').value.split('');
            for (let i = 0; i < 32; i++) {
                // جرب 0 و 1
                for (let bit of ['0', '1']) {
                    nonce[i] = bit;
                    document.getElementById('nonce').value = nonce.join('');
                    const hash = await updateHash();
                    if (hash.startsWith('0')) {
                        // ثبت هذه البتة لأنها جابت صفر في البداية
                        break;
                    }
                }
            }
        }

        document.getElementById('start').addEventListener('click', autoSearch);
        document.querySelectorAll('input').forEach(el => el.addEventListener('input', updateHash));
        updateHash();
    </script>
</body>
</html>
