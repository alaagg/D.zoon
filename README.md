<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>🧊 SAT Thermal SHA256 Miner</title>
</head>
<body>
  <h1>🧊 SAT Thermal SHA256 Miner</h1>
  <button onclick="mine()">🎯 Start Mining</button>
  <pre id="output">Waiting...</pre>

  <script type="module">
    import init, { sha256 } from 'https://cdn.jsdelivr.net/gh/denoland/deno_std@0.202.0/hash/sha256.ts'; // This line is symbolic; real sha256 done via Web Crypto API below

    async function mine() {
      const output = document.getElementById("output");

      const version = Uint8Array.from(hexToBytes("20000000"));
      const prev_hash = Uint8Array.from(hexToBytes("2e1d17b6850a8278657e7d1396d7b6cce780e93bc3d24f437c86f42cdd4c9e41")).reverse();
      const merkle_root = Uint8Array.from(hexToBytes("ea8ed1d443c721c3d92d09c59dcd62243e3a2387cc5dc9889353fbb6710f6c63")).reverse();
      const time = Uint8Array.from(hexToBytes("60d01152"));
      const bits = Uint8Array.from(hexToBytes("170d1d12"));

      const header = concatBytes(version, prev_hash, merkle_root, time, bits);

      function bitsToTarget(bits) {
        const exponent = bits[0];
        const mantissa = (bits[1] << 16) | (bits[2] << 8) | bits[3];
        return BigInt(mantissa) * (1n << (8n * (BigInt(exponent) - 3n)));
      }

      const target = bitsToTarget(bits);

      const h = await crypto.subtle.digest("SHA-256", header);
      const h_bytes = new Uint8Array(h);
      const phi = Array.from(h_bytes.slice(0, 32)).map(b => (b / 255) * Math.PI);

      let x = Array.from({ length: 32 }, () => Math.random() > 0.5 ? 1 : 0);

      function score(bits) {
        return bits.reduce((acc, bit, i) => acc + Math.sin(bit * phi[i]), 0);
      }

      for (let step = 0; step < 200; step++) {
        let best = Math.abs(score(x));
        for (let i = 0; i < 32; i++) {
          let new_x = [...x];
          new_x[i] ^= 1;
          let new_score = Math.abs(score(new_x));
          if (new_score < best) {
            x = new_x;
            best = new_score;
            break;
          }
        }
      }

      const nonce = parseInt(x.join(""), 2);
      const nonce_bytes = new Uint8Array(new Uint32Array([nonce]).buffer);
      const full_header = concatBytes(header, nonce_bytes);

      const double_hash = await crypto.subtle.digest("SHA-256", await crypto.subtle.digest("SHA-256", full_header));
      const h_final = new Uint8Array(double_hash).reverse();
      const satisfies = bytesToBigInt(h_final) < target;

      const result = {
        bits: x,
        nonce,
        hex: "0x" + nonce.toString(16).padStart(8, '0'),
        satisfies
      };

      output.textContent = JSON.stringify(result, null, 2);
    }

    // Utility functions
    function hexToBytes(hex) {
      return hex.match(/.{1,2}/g).map(b => parseInt(b, 16));
    }

    function concatBytes(...arrays) {
      return Uint8Array.from(arrays.flatMap(arr => Array.from(arr)));
    }

    function bytesToBigInt(bytes) {
      return bytes.reduce((acc, b) => (acc << 8n) | BigInt(b), 0n);
    }
  </script>
</body>
</html>
