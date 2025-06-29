<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>حلزونات A متعاكسة</title>
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
  canvas.width = window.innerWidth;
  canvas.height = window.innerHeight;
  const cx = canvas.width / 2;
  const cy = canvas.height / 2;

  // دالة التحقق من العدد A (أولي)
  function isA(n) {
    if (n < 2) return false;
    for (let i = 2; i <= Math.sqrt(n); i++) {
      if (n % i === 0) return false;
    }
    return true;
  }

  // توليد أعداد A
  const N = 1000;
  const A = [];
  for (let i = 0; i < N; i++) {
    if (isA(i)) A.push(i);
  }

  let t = 0;
  const speed = 0.01;

  function draw() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    t += speed;

    A.forEach(i => {
      const r = i * 0.6;
      const baseAngle = i * 0.1;

      // 4 حلزونات بزاوايا: 0، 90، 180، 270
      const angles = [0, Math.PI/2, Math.PI, 3*Math.PI/2];
      const colors = ['red', 'blue'];

      angles.forEach((offset, index) => {
        const sign = index % 2 === 0 ? 1 : -1; // كل زوج عكس الآخر
        const angle = baseAngle + sign * t + offset;
        const x = cx + r * Math.cos(angle);
        const y = cy + r * Math.sin(angle);
        ctx.fillStyle = colors[index % 2];
        ctx.beginPath();
        ctx.arc(x, y, 2, 0, Math.PI * 2);
        ctx.fill();
      });
    });

    requestAnimationFrame(draw);
  }

  draw();
</script>
</body>
</html>
