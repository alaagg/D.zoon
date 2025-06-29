<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>أثر دائم يلائم شاشة الهاتف</title>
  <style>
    html, body {
      margin: 0;
      padding: 0;
      overflow: hidden;
      background: black;
      height: 100%;
      width: 100%;
    }
    canvas {
      display: block;
      width: 100vw;
      height: 100vh;
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

function resizeCanvas() {
  canvas.width = window.innerWidth;
  canvas.height = window.innerHeight;
}
resizeCanvas();
window.addEventListener("resize", resizeCanvas);

let w = canvas.width;
let h = canvas.height;
const cx = () => canvas.width / 2;
const cy = () => canvas.height / 2;

// مقياس حسب أصغر بعد لتلائم الهاتف
function getScale() {
  return Math.min(canvas.width, canvas.height) / 2000;
}

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

const toggleBtn = document.getElementById("toggleBtn");
toggleBtn.onclick = () => {
  running = !running;
  toggleBtn.textContent = running ? "⏸️ إيقاف" : "▶️ تشغيل";
  if (running) draw();
};

const memory = {};

function draw() {
  if (!running) return;

  t += speed;
  const scale = getScale();

  aNumbers.forEach(i => {
    const r = i * scale * 100;
    const angle1 = i * 0.1 + t;
    const angle2 = i * 0.1 - t;

    const x1 = cx() + r * Math.cos(angle1);
    const y1 = cy() + r * Math.sin(angle1);
    const x2 = cx() + r * Math.cos(angle2);
    const y2 = cy() + r * Math.sin(angle2);

    drawAndRecord(x1, y1);
    drawAndRecord(x2, y2);
  });

  requestAnimationFrame(draw);
}

function drawAndRecord(x, y) {
  const key = Math.round(x) + "," + Math.round(y);
  memory[key] = (memory[key] || 0) + 0.01;
  const alpha = Math.min(memory[key], 1);

  ctx.fillStyle = `rgba(255,255,255,${alpha})`;
  ctx.beginPath();
  ctx.arc(x, y, 1.5, 0, Math.PI * 2); // 3 بكسل
  ctx.fill();
}

draw();
</script>
</body>
</html>
