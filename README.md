<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Phase–Spectral Correction Equations for ζ(s) Zeros</title>
  <script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
  <script id="MathJax-script" async
    src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
  </script>
  <style>
    body { font-family: sans-serif; margin: 2em; line-height: 1.4; }
    h1, h2 { color: #003366; }
    .eq-block { margin: 1.5em 0; padding: 1em; background: #f9f9f9; border-radius: 4px; }
    .label { font-weight: bold; margin-bottom: .5em; display: block; }
  </style>
</head>
<body>

  <h1>Equations for Locating Nontrivial Zeros of the Riemann Zeta–Function</h1>

  <div class="eq-block">
    <span class="label">1. Transcendental Implicit Phase Equation:</span>
    \[
      F_k(t)
      =f\,t \;+\; c\,\sin(b\,t)
      \;-\;2\pi\,k
      \;+\;a\,\ln A
      \;+\;d\,\ln(A+1)
      \;-\;\arcsin\!\Bigl(\tfrac1A\Bigr)
      \;-\;R_k(k)
      =0.
    \]
  </div>

  <div class="eq-block">
    <span class="label">2. Riemann–Siegel Core Phase:</span>
    \[
      s_k^0(A)
      =\;\tfrac12
      + i\,\frac{\,2\pi
        -\sin(\beta\,t_k)
        -\gamma\,\ln A
        +G(t_k)\,}{f},
    \]
    where
    \[
      G(t)
      =\;\frac{t}{2}\ln\!\Bigl(\frac{t}{2\pi}\Bigr)
      \;-\;\frac{t}{2}
      \;-\;\frac{\pi}{8}.
    \]
  </div>

  <div class="eq-block">
    <span class="label">3. Polynomial Correction Term:</span>
    \[
      R_k(k)
      =\sum_{n=0}^7 \alpha_n\,k^n,
      \quad
      (\alpha_0,\dots,\alpha_7)
      =(252.0678847,\,-583.7649719,\,537.6509922,\,-256.2217581,\,
      67.7865404,\,-10.0645318,\,0.7838493,\,-0.0248972).
    \]
  </div>

  <div class="eq-block">
    <span class="label">4. Spectral–Spatial Radial Correction:</span>
    \[
      \Delta s_k(r,\theta;A)
      =r\,\bigl(t_k - \Im(s_k^0(A))\bigr)\,e^{\,i\theta},
      \quad
      0\le r\le1,\;0\le\theta<2\pi.
    \]
  </div>

  <div class="eq-block">
    <span class="label">5. Final Combined Zero Location:</span>
    \[
      s_k(A,r,\theta)
      =\tfrac12
      + i\,\Im\bigl(s_k^0(A)\bigr)
      +\;\Delta s_k(r,\theta;A).
    \]
    In particular, for full correction \(r=1,\theta=\tfrac\pi2\):
    \[
      s_k = \tfrac12 + i\,t_k.
    \]
  </div>

  <p>
    <em>Parameters:</em>
    \(f=1.1,\;c=0.1,\;b=1,\;a=d=1,\;\beta=\gamma=1,\;A=10^6.\)
  </p>

</body>
</html>
