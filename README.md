<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover" />
  <title>Lughan, will you be my Valentine? 💖</title>

  <!-- Confetti -->
  <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.9.3/dist/confetti.browser.min.js"></script>

  <style>
    :root {
      --bg1: #ffd6e7;
      --bg2: #fff0f6;
      --card: #ffffffdd;
      --yes: #ff3b7a;
      --yesHover: #ff1f68;
    }

    * { box-sizing: border-box; }

    body {
      margin: 0;
      min-height: 100svh;
      display: grid;
      place-items: center;
      background: radial-gradient(circle at top, var(--bg2), var(--bg1));
      font-family: system-ui, -apple-system, sans-serif;
      overflow: hidden;
      padding: 16px;
    }

    #confettiCanvas {
      position: fixed;
      inset: 0;
      width: 100vw;
      height: 100vh;
      pointer-events: none;
      z-index: 9999;
    }

    .card {
      width: min(720px, 92vw);
      padding: 30px 22px;
      background: var(--card);
      backdrop-filter: blur(12px);
      border-radius: 24px;
      text-align: center;
      box-shadow: 0 20px 60px rgba(0,0,0,.15);
    }

    .art {
      width: min(260px, 80vw);
      margin: 0 auto 10px;
      display: block;
      filter: drop-shadow(0 10px 14px rgba(0,0,0,.12));
    }

    h1 {
      font-size: clamp(26px, 4vw, 44px);
      margin: 10px 0;
    }

    p {
      font-size: 16px;
      opacity: 0.85;
      margin-bottom: 20px;
    }

    .button-zone {
      position: relative;
      width: min(520px, 92%);
      height: 150px;
      margin: 0 auto;
      touch-action: none;
    }

    button {
      position: absolute;
      top: 50%;
      transform: translateY(-50%);
      padding: 16px 26px;
      font-size: 18px;
      font-weight: 800;
      border-radius: 999px;
      border: none;
      cursor: pointer;
      box-shadow: 0 10px 24px rgba(0,0,0,.14);
      user-select: none;
      transition: transform .12s ease, background .12s ease;
    }

    #yesBtn {
      left: 18%;
      background: var(--yes);
      color: #fff;
    }
    #yesBtn:hover { background: var(--yesHover); }

    #noBtn {
      left: 62%;
      background: #e5e7eb;
      color: #111827;
    }

    .hint {
      margin-top: 10px;
      font-size: 13px;
      opacity: .7;
    }

    .result {
      display: none;
      margin-top: 20px;
      animation: pop .35s ease;
    }

    .result h2 {
      font-size: clamp(30px, 4.5vw, 46px);
      margin-bottom: 12px;
    }

    .fireworks {
      width: min(380px, 90vw);
      border-radius: 14px;
    }

    @keyframes pop {
      from { transform: scale(.95); opacity: 0; }
      to { transform: scale(1); opacity: 1; }
    }
  </style>
</head>

<body>
  <canvas id="confettiCanvas"></canvas>

  <main class="card">
    <!-- Cute animal -->
    <svg class="art" viewBox="0 0 320 240" xmlns="http://www.w3.org/2000/svg">
      <defs>
        <linearGradient id="fur" x1="0" x2="1">
          <stop offset="0" stop-color="#f7c7a1"/>
          <stop offset="1" stop-color="#f2a97b"/>
        </linearGradient>
        <linearGradient id="heart" x1="0" x2="1">
          <stop offset="0" stop-color="#ff4d7d"/>
          <stop offset="1" stop-color="#ff1f68"/>
        </linearGradient>
      </defs>

      <path d="M250 50 C250 33 270 25 282 38
               C294 25 314 33 314 50
               C314 78 282 92 282 106
               C282 92 250 78 250 50Z"
            fill="url(#heart)"/>

      <path d="M90 120 C90 70 140 40 190 60
               C240 40 290 70 290 120
               C290 180 240 210 190 210
               C140 210 90 180 90 120Z"
            fill="url(#fur)"/>

      <circle cx="160" cy="130" r="8"/>
      <circle cx="220" cy="130" r="8"/>

      <path d="M190 144 C186 144 182 148 182 152
               C182 160 190 164 190 170
               C190 164 198 160 198 152
               C198 148 194 144 190 144Z"
            fill="#ff7aa2"/>
    </svg>

    <h1>Lughan, will you be my Valentine? 💘</h1>
    <p>
      I promise snacks, laughs, bad jokes, and making you feel special 🥺❤️
    </p>

    <section class="button-zone" id="zone">
      <button id="yesBtn">Yes 💖</button>
      <button id="noBtn">No 🙃</button>
    </section>

    <div class="hint" id="hint">You can try… but “No” is shy 😈</div>

    <section class="result" id="result">
      <h2>It’s a DATEEEEE 🎉💞</h2>
      <img
        class="fireworks"
        src="https://media.giphy.com/media/26ufdipQqU2lhNA4g/giphy.gif"
        alt="Celebration"
      />
    </section>
  </main>

  <script>
    const zone = document.getElementById("zone");
    const yesBtn = document.getElementById("yesBtn");
    const noBtn = document.getElementById("noBtn");
    const result = document.getElementById("result");
    const hint = document.getElementById("hint");

    const confettiCanvas = document.getElementById("confettiCanvas");
    const confettiInstance = confetti.create(confettiCanvas, { resize: true });

    let yesScale = 1;
    function growYes() {
      yesScale = Math.min(2.3, yesScale + 0.12);
      yesBtn.style.transform = `translateY(-50%) scale(${yesScale})`;
    }

    function clamp(n, min, max) {
      return Math.max(min, Math.min(max, n));
    }

    function moveNo(px, py) {
      const z = zone.getBoundingClientRect();
      const b = noBtn.getBoundingClientRect();

      let dx = (b.left + b.width / 2) - px;
      let dy = (b.top + b.height / 2) - py;
      let mag = Math.hypot(dx, dy) || 1;
      dx /= mag;
      dy /= mag;

      let newLeft = (b.left - z.left) + dx * 160;
      let newTop = (b.top - z.top) + dy * 160;

      newLeft = clamp(newLeft, 0, z.width - b.width);
      newTop = clamp(newTop, 0, z.height - b.height);

      noBtn.style.left = newLeft + "px";
      noBtn.style.top = newTop + "px";
      noBtn.style.transform = "none";

      growYes();
    }

    zone.addEventListener("pointermove", e => {
      const b = noBtn.getBoundingClientRect();
      const d = Math.hypot(
        (b.left + b.width / 2) - e.clientX,
        (b.top + b.height / 2) - e.clientY
      );
      if (d < 140) moveNo(e.clientX, e.clientY);
    });

    yesBtn.addEventListener("click", () => {
      zone.style.display = "none";
      hint.style.display = "none";
      result.style.display = "block";

      confettiInstance({
        particleCount: 300,
        spread: 140,
        startVelocity: 60,
        origin: { x: 0.5, y: 0.6 }
      });
    });
  </script>
</body>
</html>
