<!DOCTYPE html><html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Blockchain Miner Web Interface</title>
  <script src="https://cdn.jsdelivr.net/pyodide/v0.23.4/full/pyodide.js"></script>
  <style>
    body { font-family: monospace; background: #0f0f0f; color: #00ff66; padding: 20px; }
    input, button { padding: 8px; margin: 5px 0; width: 100%; font-family: monospace; }
    button { background: #00ffaa; border: none; font-weight: bold; cursor: pointer; }
    .section { margin-bottom: 20px; }
    .output { background: #111; padding: 15px; white-space: pre-wrap; border-left: 4px solid #00ffaa; margin-top: 10px; }
  </style>
</head>
<body>
  <h1>🚀 Blockchain Mining Web Interface</h1>  <div class="section">
    <button onclick="fetchLatestBlock()">🌐 استيراد آخر بلوك من الشبكة</button>
    <label>Block Number:</label>
    <input id="block_number" type="number" value="">
    <label>Timestamp (Unix):</label>
    <input id="timestamp" type="number" value="">
    <label>Merkle Root:</label>
    <input id="merkle_root" type="text" value="">
    <label>Previous Block Hash:</label>
    <input id="prev_hash" type="text" value="">
    <label>Difficulty (Bits):</label>
    <input id="difficulty_bits" type="number" value="20">
    <label>Nonce (manual or leave blank):</label>
    <input id="manual_nonce" type="text" value="">
    <button onclick="startMining()">🔍 ابدأ التعدين</button>
  </div>  <div class="output" id="output">⛏️ جاهز للتعدين...</div>  <script>
    async function fetchLatestBlock() {
      const response = await fetch("https://blockstream.info/api/blocks");
      const blocks = await response.json();
      const latest = blocks[0];

      document.getElementById("block_number").value = latest.height + 1;
      document.getElementById("timestamp").value = Math.floor(Date.now() / 1000);
      document.getElementById("prev_hash").value = latest.id;
      document.getElementById("merkle_root").value = "";
    }

    async function startMining() {
      const output = document.getElementById("output");
      output.textContent = "🔄 جاري تحميل المحرك...";
      let pyodide = await loadPyodide();
      output.textContent = "⛏️ جاري التعدين...";

      const block_number = document.getElementById("block_number").value;
      const timestamp = document.getElementById("timestamp").value;
      const merkle_root = document.getElementById("merkle_root").value;
      const prev_hash = document.getElementById("prev_hash").value;
      const difficulty_bits = parseInt(document.getElementById("difficulty_bits").value);
      const manual_nonce = document.getElementById("manual_nonce").value;

      const code = `
import hashlib, time
block_number = ${block_number}
timestamp = ${timestamp}
merkle_root = "${merkle_root}"
prev_hash = "${prev_hash}"
difficulty_bits = ${difficulty_bits}
manual_nonce = "${manual_nonce}"

def double_sha256(data):
    return hashlib.sha256(hashlib.sha256(data.encode()).digest()).hexdigest()

def mine_block():
    prefix = '0' * (difficulty_bits // 4)
    nonce = int(manual_nonce) if manual_nonce else 0
    start_time = time.time()

    while True:
        block_data = f"{block_number}{timestamp}{merkle_root}{prev_hash}{nonce}"
        block_hash = double_sha256(block_data)

        if block_hash.startswith(prefix):
            elapsed = time.time() - start_time
            print("\n✅ نونس ناجح!")
            print("Nonce:", nonce)
            print("Hash:", block_hash)
            print("⏱️ الوقت:", round(elapsed, 2), "ثانية")
            break

        if nonce % 500000 == 0:
            print(f"... Nonce = {nonce}, Hash = {block_hash[:16]}...")

        nonce += 1

mine_block()`;

      pyodide.setStdout({ batched: (msg) => output.textContent += "\n" + msg });
      pyodide.setStderr({ batched: (msg) => output.textContent += "\nخطأ: " + msg });
      await pyodide.runPythonAsync(code);
    }
  </script></body>
</html>
