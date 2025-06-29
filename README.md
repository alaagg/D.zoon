<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>أثر دائم لكل مرور فوق نقطة</title>
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
const speed = 0.005;

const toggleBtn = document.getElementById("toggleBtn");
toggleBtn.onclick = () => {
  running = !running;
  toggleBtn.textContent = running ? "⏸️ إيقاف" : "▶️ تشغيل";
  if (running) draw();
};

// مصفوفة الأثر: تحفظ عدد مرات مرور كل نقطة تقريبًا
const memory = {};

function draw() {
  if (!running) return;

  t += speed;

  aNumbers.forEach(i => {
    const r = i * 0.5;
    const angle1 = i * 0.1 + t;
    const angle2 = i * 0.1 - t;

    const x1 = cx + r * Math.cos(angle1);
    const y1 = cy + r * Math.sin(angle1);
    const x2 = cx + r * Math.cos(angle2);
    const y2 = cy + r * Math.sin(angle2);

    // نرسم كل نقطة بحجم 3 بكسل
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
  ctx.arc(x, y, 1.5, 0, Math.PI * 2); // نصف القطر 1.5 = قطر 3 بكسل
  ctx.fill();
}

draw();
</script>
</body>
</html>
