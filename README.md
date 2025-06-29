<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>تمثيل صوتي لأصفار زيتا</title>
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

// أول 10 أصفار غير بديهية لدالة زيتا (t فقط)
const zetaZeros = [
  14.1347, 21.0220, 25.0108, 30.4248, 32.9350,
  37.5861, 40.9187, 43.3270, 48.0051, 49.7738
];

// إعداد الصوت
const audioCtx = new (window.AudioContext || window.webkitAudioContext)();

function playTone(freq, duration=0.1, volume=0.3) {
  const osc = audioCtx.createOscillator();
  const gain = audioCtx.createGain();
  osc.frequency.value = freq;
  osc.type = 'sine';
  gain.gain.value = volume;
  osc.connect(gain);
  gain.connect(audioCtx.destination);
  osc.start();
  osc.stop(audioCtx.currentTime + duration);
}

let t = 0;
function draw() {
  ctx.fillStyle = "rgba(0,0,0,0.1)";
  ctx.fillRect(0, 0, w, h);

  t += 0.01;

  let interference = 0;

  aNumbers.forEach(i => {
    const r = i * 0.5;
    const angle1 = i * 0.1 + t;
    const angle2 = i * 0.1 - t;
    const x1 = cx + r * Math.cos(angle1);
    const y1 = cy + r * Math.sin(angle1);
    const x2 = cx + r * Math.cos(angle2);
    const y2 = cy + r * Math.sin(angle2);
    const dx = x1 - x2, dy = y1 - y2;
    const dist = Math.sqrt(dx*dx + dy*dy);
    if (dist < 5) interference += 1;
    
    ctx.fillStyle = "red";
    ctx.beginPath();
    ctx.arc(x1, y1, 1.5, 0, Math.PI * 2);
    ctx.fill();
    ctx.beginPath();
    ctx.arc(x2, y2, 1.5, 0, Math.PI * 2);
    ctx.fill();
  });

  // مقارنة التراكب مع الأصفار الحقيقية
  zetaZeros.forEach((zero, idx) => {
    const diff = Math.abs(t - zero);
    if (diff < 0.01) {
      playTone(400 + idx * 40, 0.15, 0.5); // نغمة تميز الصفر
    } else if (diff < 0.05) {
      playTone(200 + idx * 20, 0.05, 0.2); // نغمة خفيفة إن قريبة
    }
  });

  requestAnimationFrame(draw);
}

// تشغيل الصوت عند أول تفاعل
document.body.addEventListener("click", () => {
  audioCtx.resume(); // بعض المتصفحات تحتاج إذن المستخدم لتشغيل الصوت
});
draw();
</script>
</body>
</html>
