<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Alaa's Critical Phase Equation (Validated Form)</title>
  <script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
  <script id="MathJax-script" async
    src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>
  <style>
    body {
      background-color: #121212;
      color: #f1f1f1;
      font-family: 'Segoe UI', sans-serif;
      padding: 40px;
    }
    h1 {
      color: #00ffff;
      text-align: center;
    }
    .equation {
      font-size: 1.4em;
      text-align: center;
      margin: 30px 0;
    }
    .constants {
      background-color: #1e1e1e;
      padding: 20px;
      border-radius: 10px;
      width: fit-content;
      margin: auto;
    }
  </style>
</head>
<body>
  <h1>Alaa's Critical Phase Equation</h1>

  <div class="equation">
    $$ s_k(A) = \frac{1}{2} + i \cdot t_k(A) $$
    <br>
    $$ t_k(A) = \frac{2\pi k - 0.1 \cdot \sin(0.1 t) - 0.525 \cdot \ln(A) - 0.065 \cdot \ln(A + 1) + \arcsin\left(\frac{1}{A}\right)}{1.2} $$
  </div>

  <div class="constants">
    <h3>Validated Constants:</h3>
    <ul>
      <li>a = 0.525</li>
      <li>b = 0.1</li>
      <li>c = 0.1</li>
      <li>d = 0.065</li>
      <li>f = 1.2</li>
      <li>A = 999983</li>
      <li>k = 4.01327</li>
    </ul>
    <p><strong>Result:</strong> s_k = 0.5 + 14.138482i</p>
    <p><strong>Distance from true zero:</strong> 0.00376 &nbsp;&nbsp; | &nbsp;&nbsp; <strong>Error %:</strong> 0.0265%</p>
  </div>
</body>
</html>
