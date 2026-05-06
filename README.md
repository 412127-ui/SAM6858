# SAM6858<!DOCTYPE html>
<html lang="zh-Hant">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Binary Search 猜數字遊戲</title>
  <style>
    body { font-family: Arial, sans-serif; max-width: 1000px; margin: 20px auto; padding: 20px; text-align: center; background: #f8fafc; }
    h1 { color: #1e293b; }
    #grid { display: grid; grid-template-columns: repeat(10, 1fr); gap: 8px; margin-top: 20px; }
    .num { padding: 12px; background: #dbeafe; border-radius: 8px; font-weight: bold; cursor: pointer; transition: all 0.3s ease; }
    .num:hover { transform: scale(1.05); }
    .dim { opacity: 0.25; background: #e5e7eb; pointer-events: none; }
    .active { background: #93c5fd; }
    .correct { background: #22c55e; color: white; }
    #message { margin-top: 20px; font-size: 20px; font-weight: bold; }
    #range { margin-top: 10px; color: #475569; }
    #camryOverlay { position: fixed; inset: 0; background: rgba(0,0,0,0.85); display: none; justify-content: center; align-items: center; flex-direction: column; z-index: 999; }
    #camryOverlay img { max-width: 80%; border-radius: 20px; box-shadow: 0 0 40px rgba(255,255,255,0.4); animation: zoomIn 0.8s ease; }
    #camryOverlay h2 { color: white; margin-bottom: 20px; }
    @keyframes zoomIn { from { transform: scale(0.5); opacity:0; } to { transform: scale(1); opacity:1; } }
  </style>
</head>
<body>
  <h1>Binary Search 猜數字遊戲</h1>
  <p>直接點選數字猜測</p>
  <div id="range">有效搜尋範圍：1 - 100</div>
  <div id="message"></div>
  <div id="grid"></div>
  <div id="camryOverlay">
    <h2>🔥 計程車登場 🔥</h2>
    <h3 style="color:white; font-size:32px; margin-top:20px;">你們都廢物</h3>
    <img src="https://images.unsplash.com/photo-1626668893632-6f3a4466d22f?auto=format&fit=crop&w=1200&q=80" alt="計程車">
  </div>

  <script>
    let target = Math.floor(Math.random() * 100) + 1;
    let low = 1, high = 100, guesses = 0, gameOver = false;
    const grid = document.getElementById('grid');
    const message = document.getElementById('message');
    const rangeDisplay = document.getElementById('range');

    function playVictorySound() {
      const ctx = new (window.AudioContext || window.webkitAudioContext)();
      const notes = [523.25, 659.25, 783.99, 1046.5];
      notes.forEach((freq, i) => {
        const osc = ctx.createOscillator();
        const gain = ctx.createGain();
        osc.connect(gain);
        gain.connect(ctx.destination);
        osc.frequency.value = freq;
        osc.type = 'triangle';
        gain.gain.setValueAtTime(0.2, ctx.currentTime + i * 0.2);
        gain.gain.exponentialRampToValueAtTime(0.001, ctx.currentTime + i * 0.2 + 0.25);
        osc.start(ctx.currentTime + i * 0.2);
        osc.stop(ctx.currentTime + i * 0.2 + 0.25);
      });
    }

    function makeGuess(guess) {
      if (gameOver) return;
      guesses++;
      if (guess < target) {
        message.textContent = `${guess} 太小了！`;
        low = Math.max(low, guess + 1);
      } else if (guess > target) {
        message.textContent = `${guess} 太大了！`;
        high = Math.min(high, guess - 1);
      } else {
        message.textContent = `🎉 Bingo! You got it! 共猜了 ${guesses} 次`;
        document.getElementById('num-' + guess).dataset.correct = 'true';
        gameOver = true;
        playVictorySound();
        document.getElementById('camryOverlay').style.display = 'flex';
        setTimeout(resetGame, 5000);
      }
      updateVisualization();
    }

    function resetGame() {
      target = Math.floor(Math.random() * 100) + 1;
      low = 1; high = 100; guesses = 0; gameOver = false;
      message.textContent = '';
      document.getElementById('camryOverlay').style.display = 'none';
      rangeDisplay.textContent = '有效搜尋範圍：1 - 100';
      for (let i = 1; i <= 100; i++) {
        delete document.getElementById('num-' + i).dataset.correct;
      }
      updateVisualization();
    }

    function updateVisualization() {
      for (let i = 1; i <= 100; i++) {
        const el = document.getElementById('num-' + i);
        el.className = 'num';
        if (el.dataset.correct === 'true') el.classList.add('correct');
        else if (i < low || i > high) el.classList.add('dim');
        else el.classList.add('active');
      }
      if (!gameOver) rangeDisplay.textContent = `有效搜尋範圍：${low} - ${high}`;
    }

    for (let i = 1; i <= 100; i++) {
      const div = document.createElement('div');
      div.className = 'num active';
      div.id = 'num-' + i;
      div.textContent = i;
      div.onclick = () => makeGuess(i);
      grid.appendChild(div);
    }

    updateVisualization();
  </script>
</body>
</html>
