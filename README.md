<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Albasatneh RH Equation</title>
  <style>
    body {
      background-color: black;
      color: white;
      font-family: 'Courier New', monospace;
      padding: 30px;
      line-height: 1.6;
    }
    h1, h2 {
      color: #00ffff;
    }
    .section {
      margin-bottom: 30px;
    }
    .code {
      background-color: #111;
      padding: 10px;
      border-radius: 8px;
      white-space: pre-wrap;
      font-size: 16px;
      color: #0f0;
    }
    input, button {
      font-size: 18px;
      padding: 10px;
      border-radius: 6px;
      border: none;
      margin-top: 10px;
      margin-right: 10px;
    }
    input {
      width: 200px;
    }
    button {
      background-color: #00ffff;
      color: black;
      cursor: pointer;
    }
    .output {
      margin-top: 15px;
      font-size: 18px;
      color: #ff0;
    }
  </style>
</head>
<body>
  <h1>📘 Albasatneh RH Equation</h1>

  <div class="section">
    <strong>Presented by:</strong> Alaa Sheikh Albasatneh<br>
    <strong>Nationality:</strong> Syrian<br>
    <strong>Date:</strong> <span id="today-date"></span>
  </div>

  <div class="section">
    <h2>🔷 Compute Root tₖ</h2>
    <input type="number" id="k-input" placeholder="Enter k">
    <button onclick="computeTk()">Compute tₖ</button>
    <div class="output" id="tk-output"></div>
  </div>

  <div class="section">
    <h2>🔷 Equation</h2>
    <div class="code">
      tₖ = [2πk + C₀ + ∑ βₙ (ln k / ln ln k)ⁿ] / f
    </div>
  </div>

  <div class="section">
    <h2>🔷 Constants</h2>
    <div class="code">
      f = 1
      C₀ = -6.180555
      c = 0.844327
    </div>
  </div>

  <div class="section">
    <h2>🔷 βₙ Coefficients</h2>
    <div class="code">
      β = [
        +0.774963,
        -0.225223,
        +0.053304,
        -0.010113,
        +0.001562,
        -0.000200,
        +0.000020,
        +0.000002,
        +0.0000001,
        +0.00000001
      ]
    </div>
  </div>

  <script>
    // التاريخ الحالي
    document.getElementById("today-date").innerText = new Date().toDateString();

    // حساب t_k باستخدام المعادلة
    function computeTk() {
      const k = parseFloat(document.getElementById('k-input').value);
      if (isNaN(k) || k <= 0) {
        document.getElementById('tk-output').innerText = "⚠️ Please enter a valid k > 0";
        return;
      }

      const C_0 = -6.180555;
      const f = 1;
      const beta = [
        0.774963, -0.225223, 0.053304, -0.010113, 0.001562,
        -0.000200, 0.000020, 0.000002, 0.0000001, 0.00000001
      ];

      const ln_k = Math.log(k);
      const ln_ln_k = Math.log(ln_k);
      const x = ln_k / ln_ln_k;

      let R_k = 0;
      for (let n = 0; n < beta.length; n++) {
        R_k += beta[n] * Math.pow(x, n);
      }

      const tk = (2 * Math.PI * k + C_0 + R_k) / f;
      document.getElementById('tk-output').innerText = `tₖ ≈ ${tk}`;
    }
  </script>
</body>
</html>
