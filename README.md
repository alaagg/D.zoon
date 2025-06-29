<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>حلزونات A المتعاكسة</title>
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
  canvas.width = window.innerWidth;
  canvas.height = window.innerHeight;
  const cx = canvas.width / 2;
  const cy = canvas.height / 2;

  // دالة التحقق من A
  function isA(n) {
    if (n < 2) return false;
    for (let i = 2; i * i <= n; i++) {
      if (n % i === 0) return false;
    }
    return true;
  }

  // توليد A
  const N = 1500;
  const aNumbers = [];
  for (let i = 0; i < N; i++) {
    if (isA(i)) aNumbers.push(i);
  }

  let t = 0;
  let running = true;
  const speed = 0.01;

  const toggleBtn = document.getElementById("toggleBtn");
  toggleBtn.onclick = () => {
    running = !running;
    toggleBtn.textContent = running ? "⏸️ إيقاف" : "▶️ تشغيل";
    if (running) draw();
  };

  function draw() {
    if (!running) return;

    ctx.clearRect(0, 0, canvas.width, canvas.height);
    t += speed;

    for (let i of aNumbers) {
      const r = i * 0.6;
      const a1 = i * 0.1 + t;
      const a2 = i * 0.1 - t;

      const x1 = cx + r * Math.cos(a1);
      const y1 = cy + r * Math.sin(a1);
      const x2 = cx + r * Math.cos(a2);
      const y2 = cy + r * Math.sin(a2);

      ctx.fillStyle = "red";
      ctx.beginPath(); ctx.arc(x1, y1, 2, 0, Math.PI * 2); ctx.fill();
      ctx.beginPath(); ctx.arc(x2, y2, 2, 0, Math.PI * 2); ctx.fill();
    }

    requestAnimationFrame(draw);
  }

  draw();
</script>
</body>
</html>
