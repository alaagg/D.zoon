<!DOCTYPE html><html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Spectral Evolution Mining</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    body {
      background: #101010;
      color: #00ff99;
      font-family: monospace;
      padding: 20px;
    }
    input, button {
      width: 100%;
      padding: 10px;
      margin: 5px 0;
      background: #1a1a1a;
      color: #00ff99;
      border: none;
      border-radius: 5px;
    }
    button {
      background-color: #00aa00;
      cursor: pointer;
    }
    #stopBtn {
      background-color: #aa0000;
    }
    #exportBtn {
      background-color: #0055aa;
    }
    .output {
      background: #111;
      border-left: 5px solid #0f0;
      padding: 15px;
      margin-top: 15px;
      white-space: pre-wrap;
    }
    #statsDisplay {
      margin-top: 15px;
    }
  </style>
</head>
<body>
  <h1>Spectral Genetic Mining</h1>  <form id="miningForm">
    <label>Batch Size:</label>
    <input type="number" id="batchSize" value="2000000" required />
    <button type="submit" id="startBtn">Start Mining</button>
    <button type="button" id="stopBtn">Stop Mining</button>
    <button type="button" id="exportBtn">Export Results</button>
  </form>  <div class="output" id="output"></div>  <div id="statsDisplay">
    <h3>⏱️ Runtime: <span id="timer">00:00</span></h3>
    <h3>📊 Hash Count: <span id="hashCount">0</span></h3>
    <h3>✅ Successes: <span id="successCount">0</span></h3>
    <h3>🎯 Best Proximity: <span id="proximity">0%</span></h3>
    <h3>🏆 Golden Nonce: <span id="goldenNonce">—</span></h3>
  </div><canvas id="proximityChart" width="400" height="150"></canvas>

  <script>
    let mining = false;
    let timerInterval;
    let seconds = 0;
    const proximityData = [];
    const labels = [];
    const ctx = document.getElementById('proximityChart').getContext('2d');
    const chart = new Chart(ctx, {
      type: 'line',
      data: {
        labels: labels,
        datasets: [{
          label: 'Best Proximity %',
          data: proximityData,
          borderColor: '#00ff99',
          backgroundColor: 'transparent',
          tension: 0.2
        }]
      },
      options: {
        scales: {
          y: {
            beginAtZero: true,
            max: 100
          }
        }
      }
    });

    function updateStats() {
      fetch('/stats')
        .then(res => res.json())
        .then(data => {
          document.getElementById('hashCount').textContent = data.hash_count;
          document.getElementById('successCount').textContent = data.success_count;
          const prox = data.best_nonce?.proximity_percent || 0;
          document.getElementById('proximity').textContent = prox.toFixed(6) + '%';
          const nonce = data.best_nonce?.golden_nonce || '—';
          document.getElementById('goldenNonce').textContent = nonce;
          if (!labels.includes(seconds.toString())) {
            labels.push(seconds.toString());
            proximityData.push(prox);
            chart.update();
          }
        });
    }

    function startTimer() {
      seconds = 0;
      timerInterval = setInterval(() => {
        seconds++;
        const mins = String(Math.floor(seconds / 60)).padStart(2, '0');
        const secs = String(seconds % 60).padStart(2, '0');
        document.getElementById('timer').textContent = `${mins}:${secs}`;
        updateStats();
      }, 1000);
    }

    function stopTimer() {
      clearInterval(timerInterval);
    }

    document.getElementById('miningForm').addEventListener('submit', async function (e) {
      e.preventDefault();
      if (mining) return;
      mining = true;

      const batchSize = parseInt(document.getElementById('batchSize').value);
      document.getElementById('output').textContent = "⛏️ Mining started...";

      await fetch('/start', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ batchSize })
      });

      startTimer();
    });

    document.getElementById('stopBtn').addEventListener('click', async function () {
      if (!mining) return;
      await fetch('/stop', { method: 'POST' });
      mining = false;
      stopTimer();
      document.getElementById('output').textContent += "\n⛔ Mining stopped.";
    });

    document.getElementById('exportBtn').addEventListener('click', () => {
      fetch('/stats')
        .then(res => res.json())
        .then(data => {
          const blob = new Blob([JSON.stringify(data.best_nonce, null, 2)], { type: 'application/json' });
          const url = URL.createObjectURL(blob);
          const a = document.createElement('a');
          a.href = url;
          a.download = 'golden_nonce.json';
          a.click();
          URL.revokeObjectURL(url);
        });
    });
  </script></body>
</html>
