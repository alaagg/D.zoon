<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>حلزونين متعاكسين + زر توقف</title>
  <style>
    body { margin: 0; background: black; overflow: hidden; }
    canvas { display: block; margin: auto; background: black; }
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
  const speed = 0.0005;

  const memory = {}; // لتخزين آثار التطابق

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

    aNumbers.forEach(i => {
      const r = i * 0.5;
      const angle1 = i * 0.1 + t;
      const angle2 = i * 0.1 - t;

      const x1 = cx + r * Math.cos(angle1);
      const y1 = cy + r * Math.sin(angle1);
      const x2 = cx + r * Math.cos(angle2);
      const y2 = cy + r * Math.sin(angle2);

      // رسم النقاط بحجم 3 بكسل (نصف القطر 1.5)
      ctx.fillStyle = "red";
      ctx.beginPath();
      ctx.arc(x1, y1, 1.5, 0, Math.PI * 2);
      ctx.fill();

      ctx.beginPath();
      ctx.arc(x2, y2, 1.5, 0, Math.PI * 2);
      ctx.fill();

      // حساب المسافة بين النقطتين
      const dx = x1 - x2;
      const dy = y1 - y2;
      const dist = Math.sqrt(dx * dx + dy * dy);

      // إذا اقتربت النقطتان كثيرًا، أضف أثر دائم
      if (dist < 2) {
        const mx = Math.round((x1 + x2) / 2);
        const my = Math.round((y1 + y2) / 2);
        const key = mx + "," + my;
        memory[key] = (memory[key] || 0) + 0.01;
        const alpha = Math.min(memory[key], 1);

        ctx.fillStyle = `rgba(255,255,255,${alpha})`;
        ctx.beginPath();
        ctx.arc(mx, my, 2, 0, Math.PI * 2);
        ctx.fill();
      }
    });

    requestAnimationFrame(draw);
  }

  draw();
</script>
</body>
</html>
