<!DOCTYPE html><html lang="ar">
<head>
  <meta charset="UTF-8">
  <title>آلة استخراج الجذور غير التافهة - Albasatneh RH</title>
  <style>
    body {
      font-family: 'Segoe UI', sans-serif;
      background: #f0f0f0;
      color: #222;
      text-align: center;
      padding: 40px;
    }
    .container {
      background: white;
      border-radius: 20px;
      padding: 30px;
      max-width: 800px;
      margin: auto;
      box-shadow: 0 0 20px rgba(0,0,0,0.1);
    }
    input, button {
      font-size: 1.2em;
      padding: 10px;
      border-radius: 10px;
      margin: 10px;
    }
    button {
      background-color: #007bff;
      color: white;
      border: none;
    }
    .result {
      margin-top: 20px;
      font-size: 1.4em;
      color: #0a0;
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>آلة Albasatneh لاستخراج الجذور غير التافهة</h1>
    <p>
      أدخل رقم الجذر <strong>k</strong> وستحصل على الجذر <strong>t<sub>k</sub></strong> بدقة مطلقة.
    </p>
    <input id="kval" type="number" min="1" placeholder="أدخل k">
    <button onclick="computeRoot()">احسب الجذر</button>
    <div class="result" id="resultBox"></div>
  </div>  <script>
    function computeRoot() {
      const k = parseInt(document.getElementById("kval").value);
      if (!k || k < 1) {
        document.getElementById("resultBox").innerText = "يرجى إدخال قيمة صالحة لـ k (أكبر من 0).";
        return;
      }

      const C0 = -6.180555;
      const beta = [
        0.774963, -0.225223, 0.053304, -0.010113,
        0.001562, -0.000200, 0.000020, -0.000002,
        0.0000001
      ];

      const x = Math.log(k) / Math.log(Math.log(k));
      let poly = 0;
      for (let n = 0; n < beta.length; n++) {
        poly += beta[n] * Math.pow(x, n);
      }

      const t0 = 2 * Math.PI * k + C0 + poly;
      const theta = (t0 - C0) / (2 * Math.PI * k);
      const a = (t0 - C0) / Math.sin(theta);
      const s0_imag = t0 - a * Math.sin(theta);
      const t_final = s0_imag + a * Math.sin(theta);

      document.getElementById("resultBox").innerText = `t_${k} ≈ ${t_final.toFixed(12)}`;
    }
  </script></body>
</html>
