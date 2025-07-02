<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Alaa’s Critical Phase Equation – Enhanced View</title>
  <script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
  <script id="MathJax-script" async 
    src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>
  <style>
    body {
      background-color: #121212;
      color: #f0f0f0;
      font-family: 'Segoe UI', sans-serif;
      line-height: 1.8;
      padding: 40px;
    }
    h1 {
      text-align: center;
      color: #00ffcc;
      font-size: 2rem;
    }
    .section {
      background-color: #1e1e1e;
      padding: 20px;
      border-radius: 14px;
      margin: 30px auto;
      max-width: 900px;
      box-shadow: 0 0 20px rgba(0,255,255,0.15);
    }
    .section h2 {
      color: #00ffff;
      margin-bottom: 10px;
    }
  </style>
</head>
<body>

  <h1>Alaa’s Critical Phase Equation<br/>with Individual Resonance Key</h1>

  <div class="section">
    <h2>🔷 Main Equation:</h2>
    <p>
\[
      \boxed{
      s_k(A) = \frac{1}{2} + i \cdot t_k(A)
      }
\]
      <br/>
\[
      \boxed{
      t_k(A) =
      \frac{
      \underbrace{2\pi k}_{\text{Fundamental frequency}}
      - \underbrace{c \cdot \sin(b t)}_{\text{Wave ripple}}
      - \underbrace{a \cdot \ln(A)}_{\\text{Log damping}}
      - \underbrace{d \cdot \ln(A + 1)}_{\text{Fine adjustment}}
      + \underbrace{\arcsin\left( \frac{1}{A} \right)}_{\text{Edge tension}}
      + \underbrace{R_k}_{\text{Resonance key}}
      }{
      \underbrace{f}_{\text{Wave velocity}}
      }
      }
\]
    </p>
  </div>

  <div class="section">
    <h2>🔧 Constants (Guitar Structure):</h2>
    <p>
\[
      a = 0.525058, \quad
      b = 0.100077, \quad
      c = 0.100077, \quad
      d = 0.065027, \quad
      f = 1.200232
\]
    </p>
  </div>

  <div class="section">
    <h2>🎵 About R_k:</h2>
    <p>
      The value R_k is a unique correction tone for each string (each k) that ensures perfect resonance with the corresponding non-trivial Zeta zero.  
      It does not change the equation structure — it behaves like pressing a finger on the string to tune the exact tone.  
      Without R_k, the guitar plays an approximate melody — with it, the tune becomes exact.
    </p>
  </div>

</body>
</html>
