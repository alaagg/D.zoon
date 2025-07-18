<!DOCTYPE html><html lang="en">
<head>
  <meta charset="UTF-8">
  <title>🎮 Smart Nonce Miner Game</title>
  <style>
    body { font-family: sans-serif; padding: 2rem; background: #121212; color: #fff; }
    .grid { display: grid; grid-template-columns: repeat(16, 32px); gap: 4px; margin-bottom: 1rem; }
    .cell { width: 32px; height: 32px; border-radius: 4px; font-size: 13px; display: flex; align-items: center; justify-content: center; background: #444; transition: 0.3s; }
    .hot { background: #00e676; color: black; font-weight: bold; }
    .neutral { background: #888; }
    .cold { background: #ef5350; color: white; font-weight: bold; }
    button { padding: 0.6rem 1.2rem; font-size: 1rem; margin-top: 1rem; background: #1f1f1f; border: 1px solid #00e676; color: #00e676; cursor: pointer; }
    .result { font-family: monospace; margin-top: 1.2rem; font-size: 1rem; background: #1a1a1a; padding: 1rem; border-radius: 6px; border: 1px solid #333; }
  </style>
</head>
<body>
  <h2>🧠 Smart Nonce Miner Game (Auto)</h2>
  <p>هذه اللعبة تحاكي توليد نونس تلقائي استنادًا لخريطة التأثير الحراري للبتات.</p>
  <div id="bitmap" class="grid"></div>
  <div id="output" class="result">🚀 جاري المحاولة...</div>  <script>
    const influence = Array(256).fill(0).map(() => Math.random());
    const grid = document.getElementById('bitmap');

    for (let i = 0; i < 256; i++) {
      const div = document.createElement('div');
      const v = influence[i];
      div.innerText = i % 16;
      div.className = 'cell ' + (v > 0.66 ? 'hot' : v < 0.33 ? 'cold' : 'neutral');
      grid.appendChild(div);
    }

    function generateSmartNonce() {
      const bits = Array(256).fill(0);
      for (let i = 0; i < 256; i++) {
        if (influence[i] > 0.66) bits[i] = 1;
        else if (influence[i] < 0.33) bits[i] = 0;
        else bits[i] = Math.random() > 0.5 ? 1 : 0;
      }

      const bitstring = bits.join('');
      const hexNonce = BigInt('0b' + bitstring).toString(16).padStart(64, '0');
      const nonceHex = hexNonce.slice(0, 8);

      const target = BigInt("0x0000000000000000000000000000000000000000d233c95f9855bf7f9f4127c8");
      const header = '02000000' +
        'e3c8d63f2d8f0e72be1b4aa53c2fa17910bdb8b859f72e4807a2079394d4ea3e' +
        '2e3b2fcf19a3eaa232fdabc29b9de2b2e2a362a0df0d6110c4a3aeb08b32e0e3';
      const inputHex = header + nonceHex;

      fetch(`https://blockhashes.com/sha256x2/${inputHex}`)
        .then(r => r.json())
        .then(data => {
          const hashVal = BigInt('0x' + data.hash);
          const closeness = (1n - hashVal * 1000000n / target).toString();
          document.getElementById("output").innerHTML = `🔑 <b>نونس:</b> ${nonceHex}<br>
          🔁 <b>الهاش:</b> ${data.hash}<br>
          🎯 <b>القرب من التارجت:</b> ${closeness.slice(0, 5)} ppm`;
        })
        .catch(() => {
          document.getElementById("output").innerHTML = '⚠️ فشل الاتصال بحساب الهاش';
        });
    }

    // تكرار أوتوماتيكي سريع كل نصف ثانية
    setInterval(generateSmartNonce, 500);
  </script></body>
</html>
