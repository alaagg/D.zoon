<!DOCTYPE html><html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Critical Phase Equation</title>
  <!-- Load MathJax for rendering LaTeX equations -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/3.2.2/es5/tex-mml-chtml.js" integrity="sha512-R1LPBYlliFz+lznwOHzgB5ZnNtJb1kz/8voD+IsWlMHuwJKou5DFZ7AmZ7UmsidLMsjw0qa+U+TlR5a5BMpu9w==" crossorigin="anonymous" referrerpolicy="no-referrer"></script>
  <style>
    body { font-family: Arial, sans-serif; padding: 2rem; line-height: 1.6; }
    h1 { font-size: 2rem; margin-bottom: 0.5rem; }
    .meta { font-size: 0.9rem; color: #555; margin-bottom: 1.5rem; }
    .equation { margin: 1rem 0; }
    code { background-color: #f4f4f4; padding: 0.2rem 0.4rem; border-radius: 4px; }
  </style>
</head>
<body>
  <h1>Critical Phase Equation</h1>
  <p class="meta"><strong>Author:</strong> Alaa Sheikh Albasatneh (Syrian)<br>
  <strong>Date:</strong> July 7, 2025</p>
  <p>The complete formulation for the critical phase root <code>s_k</code> on the critical line:</p>
  <div class="equation">
\[
    C = \ln(10^6) + \ln(10^6+1) - \arcsin\left(10^{-6}\right)
\]
  </div>
  <div class="equation">
\[
    \{\alpha_i\}_{i=0}^7 = (252.0678847,\,-583.7649719,\,537.6509922,\,-256.2217581,\,67.7865404,\,-10.0645318,\,0.7838493,\,-0.02489716)
\]
  </div>
  <div class="equation">
\[
    R(k) = \sum_{i=0}^7 \alpha_i\,k^i
\]
  </div>
  <div class="equation">
\[
    f_k(t) = -t + \sin t - 2\pi k + C - R(k) = 0
\]
  </div>
  <div class="equation">
\[
    f_k'(t) = -1 + \cos t
\]
  </div>
  <div class="equation">
\[
    t_0 = C - R(k) - 2\pi k
\]
  </div>
  <div class="equation">
\[
    t_{n+1} = t_n - \lambda \frac{f_k(t_n)}{f_k'(t_n)}, \quad 0<\lambda\le1
\]
  </div>
  <div class="equation">
\[
    s_k = \frac12 + i\,t_k
\]
  </div>
  <p>You can implement the Newton–Raphson iteration in JavaScript or any other language by following these formulas.</p>
</body>
</html>
