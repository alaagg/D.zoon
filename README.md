<!DOCTYPE html><html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Albasatneh RH Equation – Zeta Root Generator</title>
  <script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
  <script id="MathJax-script" async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>
  <style>
    body { font-family: Arial, sans-serif; margin: 2em; background: #f0f8ff; color: #222; }
    h1, h2 { color: #003366; }
    input, button { padding: 0.5em; font-size: 1em; margin-top: 1em; }
    #output { margin-top: 2em; font-weight: bold; font-size: 1.2em; background: #eef; padding: 1em; border-radius: 6px; }
    pre { background: #eee; padding: 1em; border-radius: 4px; overflow-x: auto; }
  </style>
</head>
<body>
  <h1>Albasatneh RH Equation</h1>
  <p><strong>Purpose:</strong> Generate non-trivial zeros of the Riemann Zeta function using a closed-form log–log equation.</p>  <h2>Equation</h2>
  <pre>
  t_k = [ 2πk + C₀ + Σ(βₙ · (ln(k)/ln(ln(k)))ⁿ) ] / f
  </pre>
  <ul>
    <li>C₀ = -6.180555</li>
    <li>f = 1</li>
    <li>β = [
