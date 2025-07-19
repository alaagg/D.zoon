<!DOCTYPE html><html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Smart Nonce Generator</title>
  <style>
    body {
      font-family: monospace;
      background: #0f0f0f;
      color: #00ff99;
      padding: 40px;
    }
    h1 {
      color: #00ffaa;
    }
    input, button {
      padding: 10px;
      margin: 10px 0;
      width: 100%;
      font-family: monospace;
      background: #111;
      color: #00ffcc;
      border: 1px solid #00ffaa;
    }
    .output {
      background: #111;
      padding: 15px;
      margin-top: 20px;
      border-left: 4px solid #00ffaa;
      white-space: pre-wrap;
    }
  </style>
</head>
<body>
  <h1>🔁 Smart Nonce Generator</h1>
  <label for="targetInput">🎯 Enter Target (hex):</label>
  <input type="text" id="targetInput" placeholder="0000000000000000000d233c95f9855bf7f9f4127c8">
  <button onclick="generateNonce()">Generate Smart Nonce</button>
  <div class="output" id="output"></div>  <script>
    function hexToBinArray(hex, width = 256) {
      return BigInt('0x' + hex).toString(2).padStart(width, '0').split('').map(b => parseInt(b));
    }

    function binArrayToInt(bits) {
      return parseInt(bits.join(''), 2);
    }

    function generateNonce() {
      const targetHex = document.getElementById("targetInput").value.trim();
      if (!targetHex) return;
      const targetBigInt = BigInt('0x' + targetHex);
      const targetBits = hexToBinArray(targetHex);

      const A_bits = targetBits.slice(-32);
      const rest = Array.from({ length: 608 }, () => Math.round(Math.random()));
      const fullBlock = [...A_bits, ...rest];
      const nonceBits = fullBlock.slice(-32);
      const nonce = binArrayToInt(nonceBits);

      document.getElementById("output").textContent = `✅ Face A (from target):\n${A_bits.join('')}
\n🧊 Full Block Head (640 bits simulated)\n...\n\n🔁 Nonce (last 32 bits):\n${nonceBits.join('')}\n\n➡️ Smart Nonce = ${nonce}`;
    }
  </script></body>
</html>
