<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Alaa’s Critical Phase Equation with Resonant Key</title>
  <script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
  <script id="MathJax-script" async 
    src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>
  <style>
    body {
      background-color: #121212;
      color: #f0f0f0;
      font-family: 'Segoe UI', sans-serif;
      padding: 40px;
    }
    h1 {
      text-align: center;
      color: #00ffcc;
    }
    .formula {
      background-color: #1e1e1e;
      padding: 20px;
      border-radius: 12px;
      font-size: 1.2rem;
      margin: 20px auto;
      max-width: 800px;
      box-shadow: 0 0 12px rgba(0,255,255,0.2);
    }
  </style>
</head>
<body>
  <h1>Alaa’s Critical Phase Equation with R_k</h1>

  <div class="formula">
\[
    s_k(A) = \frac{1}{2} + i \cdot t_k(A)
\]
    <br/>
\[
    t_k(A) = \frac{
    2\pi k
    - c \cdot \sin(b \cdot t)
    - a \cdot \ln(A)
    - d \cdot \ln(A + 1)
    + \arcsin\left( \frac{1}{A} \right)
    + R_k
    }{f}
\]
  </div>

  <div class="formula">
    <strong>Constants:</strong><br/>
    a = 0.525058 &nbsp;
    b = 0.100077 &nbsp;
    c = 0.100077 <br/>
    d = 0.065027 &nbsp;
    f = 1.200232
  </div>

  <div class="formula">
    <strong>Resonance Key:</strong><br/>
    R_k is a unique correction factor for each k, used to align each frequency precisely with the true Zeta zero.
  </div>
</body>
</html>
