<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>أثر تطابق نقاط A فقط</title>
  <style>
    body { margin: 0; background: black; overflow: hidden; }
    canvas { display: block; margin: auto; background: black; }
  </style>
</head>
<body>
<canvas id="canvas"></canvas>
<script>
const canvas = document.getElementById("canvas");
const ctx = canvas.getContext("2d");
let w = canvas.width = window.innerWidth;
let h = canvas.height = window.innerHeight;
const cx = w / 2, cy = h / 2;

// توليد الأعداد A
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

// حفظ الأثر المتراكم
const memory = {};

let t = 0;
const speed = 0.001;

function draw() {
  t += speed;

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
    const dist = Math.sqrt(dx * dx + dy * dy);

    if (dist < 2) {
      const mx = Math.round((x1 + x2) / 2);
      const my = Math.round((y1 + y2) / 2);
      const key = mx + ',' + my;

      memory[key] = (memory[key] || 0) + 0.01;
      const alpha = Math.min(memory[key], 1); // لا تتجاوز الشفافية القصوى

      ctx.fillStyle = `rgba(255, 255, 255, ${alpha})`;
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
