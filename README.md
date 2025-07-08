<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Riemann Zero Model</title>
  <script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
  <script id="MathJax-script" async
    src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
  </script>
  <style>
    body { font-family: Arial, sans-serif; padding: 20px; line-height: 1.6; }
    h1 { color: #2c3e50; }
    .equation { background: #f9f9f9; border-left: 4px solid #3498db;
                padding: 10px 20px; margin: 20px 0; }
    code { background: #eee; padding: 2px 4px; border-radius: 3px; }
  </style>
</head>
<body>

  <h1>نموذج استخراج الأصفار غير البديهية لدالة زيتا</h1>

  <div class="equation">
    \[
      F_k(t)
      = f\,t
      + c\,\sin(b\,t)
      - 2\pi\,k
      + a\,\ln A
      + d\,\ln(A+1)
      - \arcsin\!\Bigl(\tfrac1A\Bigr)
      - R_k^{(\mathrm{eff})}(k)
      \;=\;0,
    \]
    حيث
    \[
      R_k^{(\mathrm{eff})}(k)
      =
      \begin{cases}
        0, & k \le K_{\mathrm{small}},\\[8pt]
        \displaystyle
        \sum_{n=0}^{m}\beta_n\,(\ln k)^n, & k > K_{\mathrm{small}}.
      \end{cases}
    \]
    عادةً نَختار \(K_{\mathrm{small}}=10\).
  </div>

  <h2>خطوات الحل الأربع</h2>
  <ol>
    <li>
      <strong>التخمين الابتدائي:</strong><br>
      \(\displaystyle t_k^{(0)} = \frac{2\pi k}{\ln\!\bigl(k/(2\pi)\bigr)}.\)
    </li>
    <li>
      <strong>تعريف الدالة ومشتقتها:</strong><br>
      \[
        F_k'(t) = f + c\,b\cos(b\,t).
      \]
    </li>
    <li>
      <strong>تكرار نيوتن–رابسون:</strong><br>
      \(\displaystyle
        t^{(m+1)} = t^{(m)} - \frac{F_k\bigl(t^{(m)}\bigr)}{F_k'\bigl(t^{(m)}\bigr)},
      \)
      حتى \(\lvert F_k(t)\rvert<10^{-14}.\)
    </li>
    <li>
      <strong>التحقّق النهائي:</strong><br>
      استعمال <code>mpmath.zeta(0.5+1j*t_k)</code> للتأكد من \(\zeta(\tfrac12+i\,t_k)\approx0\).
    </li>
  </ol>

  <h2>مثال بايثون للاختبار</h2>
  <pre><code>import mpmath as mp

mp.mp.dps = 50
f, c, b, a, d = 1.1, 0.1, 1, 1, 1
A = mp.mpf('1e6')
K_small = 10
beta = [ /* ضع معاملات β₀…βₘ هنا */ ]

def R_eff(k):
    if k <= K_small:
        return mp.mpf('0')
    L = mp.log(k)
    return sum(beta[n]*L**n for n in range(len(beta)))

def F_k(t,k):
    return (f*t + c*mp.sin(b*t) - 2*mp.pi*k
            + a*mp.log(A) + d*mp.log(A+1)
            - mp.asin(1/A) - R_eff(k))

def dF_k(t):
    return f + c*b*mp.cos(b*t)

def initial_t(k):
    return 2*mp.pi*k/mp.log(k/(2*mp.pi))

def solve_zero(k):
    t = initial_t(k)
    for _ in range(20):
        t -= F_k(t,k)/dF_k(t)
        if abs(F_k(t,k))<1e-14:
            break
    return t

# حساب الجذر k=1 كمثال
t1 = solve_zero(1)
print("t1 =", t1, " residual ζ =", abs(mp.zeta(0.5+1j*t1)))</code></pre>

</body>
</html>
