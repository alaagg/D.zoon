<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Alaa’s Zeta-Wave Equation</title>
  <script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
  <script type="text/javascript" id="MathJax-script" async
    src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
  </script>
  <style>
    body {
      background-color: #111;
      color: #eee;
      font-family: Arial, sans-serif;
      padding: 30px;
      line-height: 1.6;
    }
    h1 {
      color: #00ffd5;
    }
    .equation {
      background: #222;
      padding: 20px;
      border-radius: 8px;
      font-size: 1.2em;
      margin-top: 20px;
    }
  </style>
</head>
<body>

  <h1>Alaa’s Zeta-Wave Equation</h1>

  <p>This equation generates nontrivial zeros of the Riemann Zeta function on the critical line:</p>

  <div class="equation">
    $$ s = \frac{1}{2} + i \cdot \left( \frac{2\pi k - t - \alpha \cdot \sin(\beta t) - \gamma \cdot \ln(A+1) + \arcsin\left(\frac{\theta}{A}\right)}{f} \right) $$
  </div>

  <p>Where:</p>
  <ul>
    <li><strong>A</strong>: Natural number (acts as wave source)</li>
    <li><strong>t</strong>: Time phase</li>
    <li><strong>α, β, γ</strong>: Tuned parameters</li>
    <li><strong>θ</strong>: Phase constant (≈ 0.8)</li>
    <li><strong>f</strong>: Frequency normalization (typically 1)</li>
    <li><strong>k</strong>: Wave layer (integer)</li>
  </ul>

</body>
</html>
