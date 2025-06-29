<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>حلزونين + أثر دائم عند الالتقاء</title>
  <style>
    body { margin: 0; background: black; overflow: hidden; }
    canvas { display: block; width: 100vw; height: 100vh; background: black; }
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
  let w = canvas.width = window.innerWidth;
  let h = canvas.height = window.innerHeight;
  const cx = w / 2, cy = h / 2;

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

  // أثر دائم
  const effectCanvas = document.createElement("canvas");
  effectCanvas.width = w;
  effectCanvas.height = h;
  const effectCtx = effectCanvas.getContext("2d");

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

    // ارسم الأثر الدائم أولًا
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

      if (distance < 2) {
        // أثر دائم يُرسم مرة واحدة
        effectCtx.fillStyle = "yellow";
        effectCtx.beginPath();
        effectCtx.arc((x1 + x2) / 2, (y1 + y2) / 2, radius, 0, Math.PI * 2);
        effectCtx.fill();
      }

      // نقاط الحلزون الحالية
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
