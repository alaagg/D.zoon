<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Albasatneh – Zeta Zero Simulator</title>
  <style>
    body {
      background-color: #111;
      color: #eee;
      font-family: monospace;
      padding: 2em;
    }
    input, button {
      padding: 0.5em;
      font-size: 1em;
      background-color: #222;
      color: #eee;
      border: 1px solid #555;
      border-radius: 5px;
    }
    .result {
      margin-top: 2em;
      background: #1a1a1a;
      padding: 1em;
      border-radius: 8px;
    }
    .equation {
      font-size: 1.1em;
      background: #222;
      padding: 1em;
      border-radius: 5px;
      margin: 1em 0;
    }
  </style>
</head>
<body>
  <h1>Albasatneh – Zeta Zero Simulator</h1>
  <p>Accurate Estimation of Non-Trivial Zeta Zeros</p>

  <div class="equation">
    <b>Equation:</b><br>
    t₀(k) = 2π·k + C₀ + Σβₙ·xⁿ + α₀ + α₁·x + α₂·x²<br>
    where x = ln(k) / ln(ln(k))<br>
    C₀ = -6.180555<br>
    β = [0.774963, -0.225223, 0.053304, -0.010113, 0.001562, -0.000200, 0.000020, -0.000002, 0.0000001]
  </div>

  <label for="kval">Enter k:</label>
  <input type="number" id="kval" value="100">
  <button onclick="simulate()">Estimate Zero</button>

  <div class="result" id="output"></div>

  <script>
    function simulate() {
      const k = parseInt(document.getElementById("kval").value);
      const x = Math.log(k) / Math.log(Math.log(k));
      const beta = [0.774963, -0.225223, 0.053304, -0.010113, 0.001562, -0.000200, 0.000020, -0.000002, 0.0000001];
      let R = 0;
      for (let n = 0; n < beta.length; n++) {
        R += beta[n] * Math.pow(x, n);
      }
      const alpha0 = 0.0, alpha1 = 0.0, alpha2 = 0.0;
      const C0 = -6.180555;
      const t0 = 2 * Math.PI * k + C0 + R + alpha0 + alpha1 * x + alpha2 * x * x;

      document.getElementById("output").innerHTML = `
        <b>k = ${k}</b><br>
        Estimated t₀ ≈ ${t0.toFixed(12)}<br>
        <i>Note:</i> This is a simulated approximation only.<br>
        Newton refinement and true root require Python backend or WASM.
      `;
    }
  </script>
</body>
</html>
