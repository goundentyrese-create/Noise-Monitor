<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Stealth Protocol // Liquid Glass</title>
  <style>
    :root {
      --bg-base: #07090e;
      --glass-bg: rgba(255, 255, 255, 0.055);
      --glass-border: rgba(255, 255, 255, 0.14);
      --glass-highlight: rgba(255, 255, 255, 0.28);
      --glass-shadow: 0 24px 48px -12px rgba(0, 0, 0, 0.6), inset 0 1px 0 0 var(--glass-highlight);
      
      --accent: #34d399;
      --orb-1: rgba(16, 185, 129, 0.35);
      --orb-2: rgba(56, 189, 248, 0.25);
      
      --text: #ffffff;
      --text-secondary: rgba(255, 255, 255, 0.6);
    }

    body.warning-state {
      --accent: #fbbf24;
      --orb-1: rgba(245, 158, 11, 0.4);
      --orb-2: rgba(251, 146, 60, 0.28);
    }

    body.danger-state {
      --accent: #f87171;
      --orb-1: rgba(239, 68, 68, 0.48);
      --orb-2: rgba(244, 63, 94, 0.32);
    }

    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      font-family: -apple-system, BlinkMacSystemFont, "SF Pro Display", "Segoe UI", Roboto, sans-serif;
      -webkit-font-smoothing: antialiased;
    }

    body {
      background-color: var(--bg-base);
      color: var(--text);
      height: 100vh;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      padding: 2rem 2.5rem;
      overflow: hidden;
      position: relative;
      transition: background-color 0.3s ease;
    }

    body.alarm-flash {
      background-color: #2a080d;
    }

    /* Ambient Liquid Orbs Behind Glass */
    .ambient-canvas {
      position: fixed;
      inset: 0;
      z-index: 0;
      overflow: hidden;
      pointer-events: none;
    }

    .orb {
      position: absolute;
      border-radius: 50%;
      filter: blur(95px);
      transition: background 0.8s ease;
      animation: float 16s infinite alternate ease-in-out;
    }

    .orb-1 {
      width: 55vw;
      height: 55vw;
      max-width: 700px;
      max-height: 700px;
      background: var(--orb-1);
      top: 10%;
      left: 15%;
    }

    .orb-2 {
      width: 45vw;
      height: 45vw;
      max-width: 600px;
      max-height: 600px;
      background: var(--orb-2);
      bottom: 5%;
      right: 15%;
      animation-delay: -6s;
    }

    @keyframes float {
      0% { transform: translate(0, 0) scale(1); }
      50% { transform: translate(4%, -5%) scale(1.06); }
      100% { transform: translate(-4%, 4%) scale(0.96); }
    }

    /* Reusable Liquid Glass Surface */
    .liquid-glass {
      background: var(--glass-bg);
      backdrop-filter: blur(28px) saturate(180%);
      -webkit-backdrop-filter: blur(28px) saturate(180%);
      border: 1px solid var(--glass-border);
      box-shadow: var(--glass-shadow);
    }

    /* Header */
    header {
      position: relative;
      z-index: 2;
      display: flex;
      justify-content: space-between;
      align-items: center;
      width: 100%;
      max-width: 1040px;
      margin: 0 auto;
    }

    .glass-pill {
      display: flex;
      align-items: center;
      gap: 0.65rem;
      padding: 0.55rem 1.2rem;
      border-radius: 999px;
      font-size: 0.85rem;
      font-weight: 600;
      letter-spacing: 0.4px;
    }

    .status-dot {
      width: 9px;
      height: 9px;
      border-radius: 50%;
      background: var(--text-secondary);
      transition: all 0.3s ease;
    }

    .status-pill.active .status-dot {
      background: var(--accent);
      box-shadow: 0 0 12px var(--accent);
    }

    .strikes-label {
      font-size: 0.75rem;
      font-weight: 600;
      color: var(--text-secondary);
      letter-spacing: 0.8px;
      text-transform: uppercase;
      margin-right: 0.2rem;
    }

    .strike-dot {
      width: 10px;
      height: 10px;
      border-radius: 50%;
      background: rgba(255, 255, 255, 0.14);
      box-shadow: inset 0 1px 2px rgba(0, 0, 0, 0.4);
      transition: all 0.35s cubic-bezier(0.34, 1.56, 0.64, 1);
    }

    .strike-dot.active {
      background: #ef4444;
      box-shadow: 0 0 14px #ef4444, inset 0 1px 1px rgba(255, 255, 255, 0.6);
      transform: scale(1.25);
    }

    /* Main Glass Card Stage */
    main {
      position: relative;
      z-index: 2;
      width: 100%;
      max-width: 920px;
      margin: 0 auto;
      padding: 2.5rem 3.5rem;
      border-radius: 36px;
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 2rem;
    }

    .hero-metric {
      text-align: center;
      display: flex;
      flex-direction: column;
      align-items: center;
    }

    .percentage-display {
      font-size: clamp(4.5rem, 11vw, 8rem);
      font-weight: 700;
      letter-spacing: -3.5px;
      line-height: 0.95;
      font-variant-numeric: tabular-nums;
      background: linear-gradient(180deg, #ffffff 25%, rgba(255, 255, 255, 0.65) 100%);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      filter: drop-shadow(0 8px 24px rgba(0, 0, 0, 0.25));
    }

    .reward-capsule {
      margin-top: 1.1rem;
      display: inline-flex;
      align-items: center;
      gap: 0.5rem;
      padding: 0.4rem 1.1rem;
      border-radius: 999px;
      background: rgba(255, 255, 255, 0.06);
      border: 1px solid rgba(255, 255, 255, 0.12);
      box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.15);
    }

    .reward-tag {
      font-size: 0.72rem;
      font-weight: 700;
      letter-spacing: 0.8px;
      color: var(--accent);
      text-transform: uppercase;
    }

    .reward-input {
      background: transparent;
      border: none;
      color: var(--text);
      font-size: 0.95rem;
      font-weight: 500;
      outline: none;
      text-align: center;
      min-width: 170px;
    }

    /* Liquid Progress Track */
    .progress-track {
      width: 100%;
      height: 14px;
      background: rgba(0, 0, 0, 0.35);
      border: 1px solid rgba(255, 255, 255, 0.08);
      border-radius: 999px;
      padding: 2px;
      box-shadow: inset 0 2px 6px rgba(0, 0, 0, 0.4);
      overflow: hidden;
    }

    .progress-fill {
      height: 100%;
      width: 0%;
      background: linear-gradient(90deg, rgba(255,255,255,0.85), var(--accent));
      border-radius: 999px;
      box-shadow: 0 0 18px var(--accent), inset 0 1px 1px rgba(255, 255, 255, 0.8);
      transition: width 0.2s linear, background 0.4s ease, box-shadow 0.4s ease;
    }

    /* Visualizer */
    .visualizer-wrapper {
      width: 100%;
      height: 140px;
      position: relative;
    }

    #visualizer {
      width: 100%;
      height: 100%;
      display: block;
    }

    /* Lockout Modal */
    #lockout-overlay {
      position: fixed;
      inset: 0;
      background: rgba(5, 7, 12, 0.72);
      backdrop-filter: blur(36px) saturate(160%);
      -webkit-backdrop-filter: blur(36px) saturate(160%);
      display: none;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      z-index: 50;
      gap: 0.75rem;
    }

    .lockout-card {
      padding: 3rem 4rem;
      border-radius: 32px;
      text-align: center;
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 0.75rem;
    }

    #lockout-overlay h1 {
      color: #f87171;
      font-size: 2.2rem;
      font-weight: 700;
      letter-spacing: -0.5px;
    }

    #lockout-overlay p {
      color: var(--text-secondary);
      font-size: 1.05rem;
    }

    #cooldown-timer {
      font-size: 4.5rem;
      font-weight: 700;
      font-variant-numeric: tabular-nums;
      letter-spacing: -2px;
      margin-top: 0.5rem;
    }

    /* Floating Control Dock */
    footer {
      position: relative;
      z-index: 10;
      display: flex;
      justify-content: center;
      align-items: center;
      gap: 1.1rem;
      padding: 0.65rem 1.25rem;
      border-radius: 999px;
      width: fit-content;
      margin: 0 auto;
      opacity: 0.35;
      transform: translateY(4px);
      transition: opacity 0.35s ease, transform 0.35s cubic-bezier(0.22, 1, 0.36, 1);
    }

    footer:hover, footer.setup-mode {
      opacity: 1;
      transform: translateY(0);
    }

    .control-group {
      display: flex;
      align-items: center;
      gap: 0.55rem;
      font-size: 0.78rem;
      font-weight: 600;
      color: var(--text-secondary);
      letter-spacing: 0.4px;
    }

    .divider {
      width: 1px;
      height: 18px;
      background: rgba(255, 255, 255, 0.12);
    }

    button {
      background: rgba(255, 255, 255, 0.08);
      color: var(--text);
      border: 1px solid rgba(255, 255, 255, 0.12);
      box-shadow: inset 0 1px 0 rgba(255, 255, 255, 0.15);
      padding: 0.45rem 1rem;
      border-radius: 999px;
      cursor: pointer;
      font-size: 0.8rem;
      font-weight: 600;
      transition: all 0.2s ease;
    }

    button:hover {
      background: rgba(255, 255, 255, 0.16);
      transform: scale(1.02);
    }

    button.primary {
      background: #ffffff;
      color: #090b10;
      border: none;
      box-shadow: 0 4px 16px rgba(255, 255, 255, 0.25);
    }

    button.danger {
      color: #fca5a5;
      background: rgba(239, 68, 68, 0.15);
      border-color: rgba(239, 68, 68, 0.3);
    }

    button.danger:hover {
      background: rgba(239, 68, 68, 0.85);
      color: #ffffff;
    }

    input[type="range"] {
      -webkit-appearance: none;
      width: 88px;
      height: 5px;
      background: rgba(255, 255, 255, 0.16);
      border-radius: 999px;
      outline: none;
    }

    input[type="range"]::-webkit-slider-thumb {
      -webkit-appearance: none;
      width: 16px;
      height: 16px;
      border-radius: 50%;
      background: #ffffff;
      box-shadow: 0 2px 8px rgba(0, 0, 0, 0.4);
      cursor: pointer;
    }

    input[type="number"] {
      width: 44px;
      background: rgba(0, 0, 0, 0.25);
      color: var(--text);
      border: 1px solid rgba(255, 255, 255, 0.14);
      border-radius: 8px;
      padding: 0.3rem;
      text-align: center;
      font-size: 0.82rem;
      font-weight: 600;
      outline: none;
    }
  </style>
</head>
<body>

  <div class="ambient-canvas">
    <div class="orb orb-1"></div>
    <div class="orb orb-2"></div>
  </div>

  <header>
    <div id="status-pill" class="glass-pill liquid-glass status-pill">
      <div class="status-dot"></div>
      <span id="status-text">Standby</span>
    </div>

    <div class="glass-pill liquid-glass">
      <span class="strikes-label">Alerts</span>
      <div class="strike-dot" id="strike-1"></div>
      <div class="strike-dot" id="strike-2"></div>
      <div class="strike-dot" id="strike-3"></div>
    </div>
  </header>

  <main class="liquid-glass">
    <div class="hero-metric">
      <div class="percentage-display" id="percent-text">0.0%</div>
      <div class="reward-capsule">
        <span class="reward-tag">Target</span>
        <input type="text" class="reward-input" value="5 Mins Free Time" id="reward-text" aria-label="Reward Goal">
      </div>
    </div>

    <div class="progress-track">
      <div class="progress-fill" id="progress-fill"></div>
    </div>

    <div class="visualizer-wrapper">
      <canvas id="visualizer"></canvas>
    </div>
  </main>

  <div id="lockout-overlay">
    <div class="lockout-card liquid-glass">
      <h1>System Lockdown</h1>
      <p>Noise threshold breached. Decryption paused.</p>
      <div id="cooldown-timer">00:10</div>
    </div>
  </div>

  <footer id="control-dock" class="liquid-glass setup-mode">
    <button id="start-btn" class="primary">Start Mic</button>
    <button id="reset-btn">Reset</button>

    <div class="divider"></div>

    <div class="control-group">
      <label for="threshold">Limit</label>
      <input type="range" id="threshold" min="10" max="90" value="45">
    </div>

    <div class="control-group">
      <label for="duration">Mins</label>
      <input type="number" id="duration" min="1" max="60" value="10">
    </div>

    <div class="divider"></div>

    <button id="manual-strike-btn" class="danger">Alert</button>
  </footer>

  <script>
    let audioCtx, analyser, dataArray, source;
    let isRunning = false;
    let isLockedOut = false;
    let progress = 0;
    let strikes = 0;
    let lastStrikeTime = 0;
    let lastFrameTime = Date.now();

    const canvas = document.getElementById('visualizer');
    const ctx = canvas.getContext('2d');
    const statusPill = document.getElementById('status-pill');
    const statusText = document.getElementById('status-text');
    const progressFill = document.getElementById('progress-fill');
    const percentText = document.getElementById('percent-text');
    const thresholdSlider = document.getElementById('threshold');
    const durationInput = document.getElementById('duration');
    const lockoutOverlay = document.getElementById('lockout-overlay');
    const cooldownTimer = document.getElementById('cooldown-timer');
    const controlDock = document.getElementById('control-dock');
    const startBtn = document.getElementById('start-btn');

    function resizeCanvas() {
      canvas.width = canvas.offsetWidth;
      canvas.height = canvas.offsetHeight;
    }
    window.addEventListener('resize', resizeCanvas);
    resizeCanvas();

    // Exact mic initialization from v1
    startBtn.addEventListener('click', async () => {
      if (isRunning) return;
      try {
        const stream = await navigator.mediaDevices.getUserMedia({ audio: true });
        audioCtx = new (window.AudioContext || window.webkitAudioContext)();
        analyser = audioCtx.createAnalyser();
        analyser.fftSize = 128;
        source = audioCtx.createMediaStreamSource(stream);
        source.connect(analyser);
        dataArray = new Uint8Array(analyser.frequencyBinCount);
        
        isRunning = true;
        lastFrameTime = Date.now();
        resizeCanvas();
        startBtn.textContent = 'Listening';
        startBtn.classList.remove('primary');
        controlDock.classList.remove('setup-mode');
        updateStatus('Stealth Optimal', 'active');
        loop();
      } catch (err) {
        alert('Microphone access is required for the noise monitor to work.');
      }
    });

    document.getElementById('reset-btn').addEventListener('click', () => {
      progress = 0;
      strikes = 0;
      isLockedOut = false;
      lockoutOverlay.style.display = 'none';
      updateStrikesUI();
      updateProgressUI();
      if (isRunning) updateStatus('Stealth Optimal', 'active');
    });

    document.getElementById('manual-strike-btn').addEventListener('click', () => {
      if (isRunning && !isLockedOut) triggerStrike();
    });

    function updateStatus(text, state) {
      statusText.textContent = text;
      statusPill.className = 'glass-pill liquid-glass status-pill ' + state;
      
      document.body.classList.remove('warning-state', 'danger-state');
      if (state === 'warning') document.body.classList.add('warning-state');
      if (state === 'danger') document.body.classList.add('danger-state');
    }

    function updateStrikesUI() {
      for (let i = 1; i <= 3; i++) {
        const dot = document.getElementById(`strike-${i}`);
        if (i <= strikes) dot.classList.add('active');
        else dot.classList.remove('active');
      }
    }

    function updateProgressUI() {
      const clamped = Math.min(100, Math.max(0, progress));
      progressFill.style.width = `${clamped}%`;
      percentText.textContent = `${clamped.toFixed(1)}%`;

      if (clamped >= 100) {
        updateStatus('Target Unlocked', 'active');
      }
    }

    function triggerStrike() {
      const now = Date.now();
      if (now - lastStrikeTime < 2000) return;
      lastStrikeTime = now;

      strikes++;
      updateStrikesUI();

      document.body.classList.add('alarm-flash');
      setTimeout(() => document.body.classList.remove('alarm-flash'), 250);

      progress = Math.max(0, progress - 2);
      updateProgressUI();

      if (strikes >= 3) {
        startLockout();
      } else {
        updateStatus('Noise Spike', 'danger');
      }
    }

    function startLockout() {
      isLockedOut = true;
      lockoutOverlay.style.display = 'flex';
      let secondsLeft = 10;
      cooldownTimer.textContent = `00:${secondsLeft}`;

      const interval = setInterval(() => {
        secondsLeft--;
        cooldownTimer.textContent = `00:${secondsLeft < 10 ? '0' : ''}${secondsLeft}`;
        if (secondsLeft <= 0) {
          clearInterval(interval);
          isLockedOut = false;
          strikes = 0;
          updateStrikesUI();
          lockoutOverlay.style.display = 'none';
          updateStatus('Stealth Optimal', 'active');
        }
      }, 1000);
    }

    function loop() {
      requestAnimationFrame(loop);
      const now = Date.now();
      const deltaSeconds = (now - lastFrameTime) / 1000;
      lastFrameTime = now;

      analyser.getByteFrequencyData(dataArray);

      let sum = 0;
      const usableBins = Math.floor(dataArray.length * 0.65);
      for (let i = 0; i < usableBins; i++) {
        sum += dataArray[i];
      }
      const averageVolume = (sum / usableBins) / 2.55;
      const threshold = parseInt(thresholdSlider.value, 10);

      ctx.clearRect(0, 0, canvas.width, canvas.height);

      // Threshold line
      const threshY = canvas.height - (threshold / 100) * canvas.height;
      ctx.beginPath();
      ctx.moveTo(0, threshY);
      ctx.lineTo(canvas.width, threshY);
      ctx.strokeStyle = 'rgba(255, 255, 255, 0.22)';
      ctx.setLineDash([4, 6]);
      ctx.lineWidth = 1.5;
      ctx.stroke();
      ctx.setLineDash([]);

      // Translucent Glass Frequency Bars
      const gap = 7;
      const totalBars = usableBins;
      const barWidth = Math.max(2, (canvas.width - (gap * (totalBars - 1))) / totalBars);

      for (let i = 0; i < totalBars; i++) {
        const value = dataArray[i] / 255;
        const barHeight = Math.max(8, value * canvas.height);
        const x = i * (barWidth + gap);
        const y = canvas.height - barHeight;

        const gradient = ctx.createLinearGradient(0, y, 0, canvas.height);
        if (averageVolume > threshold) {
          gradient.addColorStop(0, 'rgba(248, 113, 113, 0.95)');
          gradient.addColorStop(1, 'rgba(239, 68, 68, 0.25)');
        } else if (averageVolume > threshold * 0.8) {
          gradient.addColorStop(0, 'rgba(251, 191, 36, 0.95)');
          gradient.addColorStop(1, 'rgba(245, 158, 11, 0.25)');
        } else {
          gradient.addColorStop(0, 'rgba(255, 255, 255, 0.85)');
          gradient.addColorStop(1, 'rgba(52, 211, 153, 0.25)');
        }

        ctx.fillStyle = gradient;
        ctx.beginPath();
        ctx.roundRect(x, y, barWidth, barHeight, 6);
        ctx.fill();
      }

      // Game Logic
      if (!isLockedOut && progress < 100) {
        if (averageVolume > threshold) {
          triggerStrike();
        } else if (averageVolume > threshold * 0.8) {
          updateStatus('Approaching Limit', 'warning');
        } else {
          updateStatus('Stealth Optimal', 'active');
          const totalSecondsNeeded = parseInt(durationInput.value, 10) * 60;
          progress += (deltaSeconds / totalSecondsNeeded) * 100;
          updateProgressUI();
        }
      }
    }
  </script>
</body>
</html>
