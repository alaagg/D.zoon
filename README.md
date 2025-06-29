<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>حلزونات A للهاتف</title>
  <style>
    body { margin: 0; background: black; overflow: hidden; }
    canvas { display: block; background: black; }
    #controls {
      position: fixed;
      top: 10px;
      left: 10px;
      z-index: 10;
      color: white;
      font-family: sans-serif;
    }
    button {
      font-size: 16px;
      padding: 6px 12px;
      margin-bottom: 10px;
    }
  </style>
</head>
<body>
<canvas id="canvas"></canvas>
<div id="controls">
  <button id="toggleBtn">⏸️ إيقاف</button><br>
  زاوية أحمر: <span id="angleRed">0</span>°<br>
  زاوية أزرق: <span id="angleBlue">0</span>°
</div>

<script>
  const canvas = document.getElementById("canvas");
  const ctx = canvas.getContext("2d");

  function resizeCanvas() {
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;
  }
  window.addEventListener('resize', resizeCanvas);
  resizeCanvas();

  const cx = () => canvas.width / 2;
  const cy = () => canvas.height / 2.2; // نرفع المركز قليلًا للأعلى

  function isA(n) {
    if (n < 2) return false;
    for (let i = 2; i <= Math.sqrt(n); i++) {
      if (n % i === 0) return false;
    }
    return true;
  }

  const N = 1000;
  const A = [];
  for (let i = 0; i < N; i++) {
    if (isA(i)) A.push(i);
  }

  let t = 0;
  let running = true;
  const speed = 0.003;
  const toggleBtn = document.getElementById("toggleBtn");
  const angleRedDisplay = document.getElementById("angleRed");
  const angleBlueDisplay = document.getElementById("angleBlue");

  toggleBtn.onclick = () => {
    running = !running;
    toggleBtn.textContent = running ? "⏸️ إيقاف" : "▶️ تشغيل";
    if (running) draw();
  };

  function draw() {
    if (!running) return;

    ctx.clearRect(0, 0, canvas.width, canvas.height);
    t += speed;

    const scale = Math.min(canvas.width, canvas.height) * 0.1 / 50;

    let lastAngleRed = 0;
    let lastAngleBlue = 0;

    A.forEach(i => {
      const r = i * scale;
      const baseAngle = i * 0.1;
      const angles = [0, Math.PI/2, Math.PI, 3*Math.PI/2];
      const colors = ['red', 'blue'];

      angles.forEach((offset, index) => {
        const sign = index % 2 === 0 ? 1 : -1;
        const angle = baseAngle + sign * t + offset;
        const x = cx() + r * Math.cos(angle);
        const y = cy() + r * Math.sin(angle);
        ctx.fillStyle = colors[index % 2];
        ctx.beginPath();
        ctx.arc(x, y, 1.4, 0, Math.PI * 2);
        ctx.fill();

        if (index === 0) lastAngleRed = angle;
        if (index === 1) lastAngleBlue = angle;
      });
    });

    angleRedDisplay.textContent = (lastAngleRed * 180 / Math.PI % 360).toFixed(2);
    angleBlueDisplay.textContent = (lastAngleBlue * 180 / Math.PI % 360).toFixed(2);

    requestAnimationFrame(draw);
  }

  draw();
</script>
</body>
</html>
