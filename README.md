<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>أثر دائم عند الملامسة فقط</title>
  <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no" />
  <style>
    html, body {
      margin: 0;
      padding: 0;
      background: black;
      overflow: hidden;
      height: 100%;
      width: 100%;
    }
    canvas {
      display: block;
      background: black;
    }
    button {
      position: fixed;
      top: 20px;
      left: 20px;
      padding: 10px 20px;
      font-size: 16px;
      z-index: 10;
    }
  </style>
</head>
<body>
<canvas id="canvas"></canvas>
<button id="toggleBtn">⏸️ إيقاف</button>

<script>
  const canvas = document.getElementById("canvas");
  const ctx = canvas.getContext("2d");

  const effectCanvas = document.createElement("canvas");
  const effectCtx = effectCanvas.getContext("2d");

  let w, h, cx, cy;
  function resizeCanvas() {
    w = canvas.width = window.innerWidth;
    h = canvas.height = window.innerHeight;
    effectCanvas.width = w;
    effectCanvas.height = h;
    cx = w / 2;
    cy = h / 2;
  }
  resizeCanvas();
  window.addEventListener('resize', resizeCanvas);

  function isA(n) {
    if (n < 2) return false;
    for (let i = 2; i * i <= n; i++) {
      if (n % i === 0) return false;
    }
    return true;
  }

  const N = 1000;
  const aNumbers = [];
  for (let i = 0; i < N; i++) {
    if (isA(i)) aNumbers.push(i);
  }

  let t = 0;
  let running = true;
  const speed = 0.005;
  const radius = 3;
  const touchThreshold = 4;

  const toggleBtn = document.getElementById("toggleBtn");
  toggleBtn.onclick = () => {
    running = !running;
    toggleBtn.textContent = running ? "⏸️ إيقاف" : "▶️ تشغيل";
    if (running) draw();
  };

  function draw() {
    if (!running) return;
    ctx.clearRect(0, 0, w, h);
    t += speed;

    // رسم الأثر الدائم أولاً
    ctx.drawImage(effectCanvas, 0, 0);

    aNumbers.forEach(i => {
      const r = i * 0.5;
      const angle1 = i * 0.1 + t;
      const angle2 = i * 0.1 - t;

      const x1 = cx + r * Math.cos(angle1);
      const y1 = cy + r * Math.sin(angle1);
      const x2 = cx + r * Math.cos(angle2);
      const y2 = cy + r * Math.sin(angle2);

      const dx = x1 - x2;
      const dy = y1 - y2;
      const distance = Math.sqrt(dx * dx + dy * dy);

      if (distance < touchThreshold) {
        const ex = (x1 + x2) / 2;
        const ey = (y1 + y2) / 2;
        effectCtx.fillStyle = "yellow";
        effectCtx.beginPath();
        effectCtx.arc(ex, ey, radius, 0, Math.PI * 2);
        effectCtx.fill();
      }

      ctx.fillStyle = "red";
      ctx.beginPath();
      ctx.arc(x1, y1, radius, 0, Math.PI * 2);
      ctx.fill();

      ctx.beginPath();
      ctx.arc(x2, y2, radius, 0, Math.PI * 2);
      ctx.fill();
    });

    requestAnimationFrame(draw);
  }

  draw();
</script>
</body>
</html>
