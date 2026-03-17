+++
title = "Daramel"
+++

<link href="https://fonts.googleapis.com/css2?family=Agbalumo&display=swap" rel="stylesheet">

<style>
#daramel-game-wrap {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 1.5rem 0;
  font-family: 'Agbalumo', cursive;
}
#daramel-canvas {
  border: 2.5px solid #5A2B22;
  border-radius: 12px;
  background: #FFF4E1;
  display: block;
  cursor: pointer;
  max-width: 100%;
}
#daramel-score-bar {
  display: flex;
  justify-content: space-between;
  width: 100%;
  max-width: 520px;
  margin-bottom: 8px;
  padding: 0 4px;
  font-size: 15px;
  font-weight: 600;
  color: #5A2B22;
}
#daramel-hint {
  margin-top: 10px;
  font-size: 13px;
  color: #5A2B22;
}

#daramel-canvas {
  border: 2.5px solid #5A2B22;
  border-radius: 12px;
  background: #FFF4E1;
  display: block;
  cursor: pointer;
  max-width: 100%;
  width: 100%;
  height: auto;
}
</style>

<div id="daramel-game-wrap" style="margin-top: 8rem; margin-bottom: 3rem; position: relative; z-index: 10000;">
  <div id="daramel-score-bar">
    <span>score: <span id="d-score">0</span></span>
    <span>best: <span id="d-best">0</span></span>
  </div>
  <canvas id="daramel-canvas" width="520" height="180"></canvas>
  <div id="daramel-hint">space / tap to jump &nbsp;·&nbsp; double jump allowed!</div>
</div>

<script>
(function() {
  const canvas = document.getElementById('daramel-canvas');
  const ctx = canvas.getContext('2d');
  const scoreEl = document.getElementById('d-score');
  const bestEl = document.getElementById('d-best');

  const W = canvas.width;
  const H = canvas.height;
  function resizeCanvas() {
  const wrap = document.getElementById('daramel-game-wrap');
  const ratio = canvas.height / canvas.width;
  canvas.style.width = Math.min(520, wrap.clientWidth) + 'px';
  canvas.style.height = (Math.min(520, wrap.clientWidth) * ratio) + 'px';
}
resizeCanvas();
window.addEventListener('resize', resizeCanvas);
  const GROUND = H - 30;
  const COLORS = {
    bg: '#FFF4E1',
    ground: '#D8A79E',
    maroon: '#5A2B22',
    date: '#8B4513',
    dateSkin: '#C4874F',
    dateHighlight: '#E8A96A',
    sugar: '#ffffff',
    sugarBorder: '#5A2B22',
    text: '#5A2B22',
    overlay: 'rgba(255,244,225,0.88)'
  };

  let state, player, obstacles, score, best, speed, frameCount, animFrame, spawnTimer;
  best = 0;

  function initGame() {
    state = 'idle';
    score = 0;
    speed = 6;
    frameCount = 0;
    spawnTimer = 0;
    obstacles = [];
    player = {
      x: 70,
      y: GROUND,
      w: 28,
      h: 32,
      vy: 0,
      jumps: 0,
      maxJumps: 2,
      onGround: true,
      frame: 0,
      frameTick: 0
    };
  }

  function jump() {
    if (state === 'idle') { state = 'running'; return; }
    if (state === 'over') { initGame(); state = 'running'; return; }
    if (player.jumps < player.maxJumps) {
      player.vy = -11;
      player.jumps++;
      player.onGround = false;
    }
  }

  document.addEventListener('keydown', e => { if (e.code === 'Space') { e.preventDefault(); jump(); }});
  canvas.addEventListener('click', jump);
  canvas.addEventListener('touchstart', e => { e.preventDefault(); jump(); }, { passive: false });

  function drawGround() {
    ctx.fillStyle = COLORS.ground;
    ctx.fillRect(0, GROUND + 20, W, 10);
    ctx.fillStyle = '#C4874F';
    ctx.fillRect(0, GROUND + 20, W, 2);
  }

  function drawDate(x, y, w, h, running, frame) {
    const cx = x + w / 2;
    const cy = y + h / 2;

    ctx.fillStyle = COLORS.dateSkin;
    ctx.beginPath();
    ctx.ellipse(cx, cy - 4, w * 0.42, h * 0.44, 0, 0, Math.PI * 2);
    ctx.fill();

    ctx.fillStyle = COLORS.date;
    ctx.beginPath();
    ctx.ellipse(cx, cy - 4, w * 0.28, h * 0.32, 0, 0, Math.PI * 2);
    ctx.fill();

    ctx.fillStyle = COLORS.dateHighlight;
    ctx.beginPath();
    ctx.ellipse(cx - 4, cy - 8, 4, 6, -0.5, 0, Math.PI * 2);
    ctx.fill();

    ctx.strokeStyle = '#5A2B22';
    ctx.lineWidth = 1.5;
    ctx.beginPath();
    ctx.moveTo(cx, y);
    ctx.quadraticCurveTo(cx + 8, y - 10, cx + 4, y - 16);
    ctx.stroke();

    ctx.fillStyle = '#4a7c4e';
    ctx.beginPath();
    ctx.ellipse(cx + 4, y - 17, 5, 3, -0.8, 0, Math.PI * 2);
    ctx.fill();

    if (running) {
      const legSwing = Math.sin(frame * 0.4) * 6;
      ctx.strokeStyle = COLORS.date;
      ctx.lineWidth = 3;
      ctx.lineCap = 'round';

      ctx.beginPath();
      ctx.moveTo(cx - 4, y + h * 0.3);
      ctx.lineTo(cx - 4 - legSwing, y + h * 0.55);
      ctx.stroke();

      ctx.beginPath();
      ctx.moveTo(cx + 4, y + h * 0.3);
      ctx.lineTo(cx + 4 + legSwing, y + h * 0.55);
      ctx.stroke();

      const armSwing = Math.sin(frame * 0.4) * 5;
      ctx.strokeStyle = COLORS.dateSkin;
      ctx.lineWidth = 2.5;

      ctx.beginPath();
      ctx.moveTo(cx - 8, cy - 2);
      ctx.lineTo(cx - 14, cy - 2 + armSwing);
      ctx.stroke();

      ctx.beginPath();
      ctx.moveTo(cx + 8, cy - 2);
      ctx.lineTo(cx + 14, cy - 2 - armSwing);
      ctx.stroke();
    }

    ctx.fillStyle = '#3a1a0a';
    ctx.beginPath();
    ctx.arc(cx - 6, cy - 8, 2, 0, Math.PI * 2);
    ctx.fill();
    ctx.beginPath();
    ctx.arc(cx + 6, cy - 8, 2, 0, Math.PI * 2);
    ctx.fill();

    ctx.strokeStyle = '#3a1a0a';
    ctx.lineWidth = 1.2;
    ctx.beginPath();
    ctx.arc(cx, cy - 2, 5, 0.2, Math.PI - 0.2);
    ctx.stroke();
  }

function drawBulbul(ob) {
    const x = ob.x, y = ob.y, w = ob.w, h = ob.h;
    const cx = x + w / 2;
    const cy = y + h / 2;
    const moving = ob.moving !== false;

    // Body
    ctx.fillStyle = '#9E9E9E';
    ctx.beginPath();
    ctx.ellipse(cx, cy + 2, w * 0.38, h * 0.28, 0.2, 0, Math.PI * 2);
    ctx.fill();

    // Wing
    ctx.fillStyle = '#757575';
    ctx.beginPath();
    ctx.ellipse(cx + 2, cy + 4, w * 0.28, h * 0.18, 0.3, 0, Math.PI * 2);
    ctx.fill();

    // Yellow rump/tail patch
    ctx.fillStyle = '#FFD600';
    ctx.beginPath();
    ctx.ellipse(cx + w * 0.32, cy + 6, w * 0.12, h * 0.1, 0.1, 0, Math.PI * 2);
    ctx.fill();

    // Tail feathers
    ctx.fillStyle = '#616161';
    ctx.beginPath();
    ctx.moveTo(cx + w * 0.3, cy + 4);
    ctx.lineTo(cx + w * 0.58, cy - 2);
    ctx.lineTo(cx + w * 0.55, cy + 2);
    ctx.lineTo(cx + w * 0.58, cy + 6);
    ctx.lineTo(cx + w * 0.3, cy + 8);
    ctx.closePath();
    ctx.fill();

    // Black head
    ctx.fillStyle = '#1A1A1A';
    ctx.beginPath();
    ctx.ellipse(cx - w * 0.22, cy - h * 0.18, w * 0.2, h * 0.2, -0.1, 0, Math.PI * 2);
    ctx.fill();

    // White cheek patch
    ctx.fillStyle = '#F5F5F5';
    ctx.beginPath();
    ctx.ellipse(cx - w * 0.28, cy - h * 0.08, w * 0.1, h * 0.1, 0, 0, Math.PI * 2);
    ctx.fill();

    // Beak — open if eating, closed if flying
    ctx.fillStyle = '#424242';
    if (ob.chomping) {
      // Open beak
      ctx.beginPath();
      ctx.moveTo(cx - w * 0.42, cy - h * 0.18);
      ctx.lineTo(cx - w * 0.62, cy - h * 0.12);
      ctx.lineTo(cx - w * 0.42, cy - h * 0.1);
      ctx.closePath();
      ctx.fill();
      ctx.beginPath();
      ctx.moveTo(cx - w * 0.42, cy - h * 0.18);
      ctx.lineTo(cx - w * 0.62, cy - h * 0.22);
      ctx.lineTo(cx - w * 0.42, cy - h * 0.24);
      ctx.closePath();
      ctx.fill();
    } else {
      ctx.beginPath();
      ctx.moveTo(cx - w * 0.42, cy - h * 0.18);
      ctx.lineTo(cx - w * 0.62, cy - h * 0.17);
      ctx.lineTo(cx - w * 0.42, cy - h * 0.12);
      ctx.closePath();
      ctx.fill();
    }

    // Eye
    ctx.fillStyle = '#ffffff';
    ctx.beginPath();
    ctx.arc(cx - w * 0.26, cy - h * 0.2, 2.5, 0, Math.PI * 2);
    ctx.fill();
    ctx.fillStyle = '#1A1A1A';
    ctx.beginPath();
    ctx.arc(cx - w * 0.27, cy - h * 0.21, 1.2, 0, Math.PI * 2);
    ctx.fill();

    // Legs
    ctx.strokeStyle = '#424242';
    ctx.lineWidth = 1.5;
    ctx.lineCap = 'round';
    const legSwing = moving ? Math.sin((ob.legFrame || 0) * 0.4) * 4 : 0;
    ctx.beginPath();
    ctx.moveTo(cx - 4, cy + h * 0.26);
    ctx.lineTo(cx - 4 - legSwing, cy + h * 0.44);
    ctx.stroke();
    ctx.beginPath();
    ctx.moveTo(cx + 4, cy + h * 0.26);
    ctx.lineTo(cx + 4 + legSwing, cy + h * 0.44);
    ctx.stroke();

    if (ob.legFrame !== undefined) ob.legFrame++;
  }

  function spawnObstacle() {
    const types = [
      { w: 22, h: 22 },
      { w: 22, h: 44 },
      { w: 44, h: 22 },
    ];
    const t = types[Math.floor(Math.random() * types.length)];
    obstacles.push({
      x: W + 10,
      y: GROUND + 20 - t.h,
      w: t.w,
      h: t.h,
      legFrame: 0
    });
  }

  function collides(a, b) {
    const pad = 5;
    return a.x + pad < b.x + b.w - pad &&
           a.x + a.w - pad > b.x + pad &&
           a.y + pad < b.y + b.h - pad &&
           a.y + a.h - pad > b.y + pad;
  }

  function drawIdle() {
    ctx.fillStyle = COLORS.bg;
    ctx.fillRect(0, 0, W, H);
    drawGround();
    drawDate(player.x, player.y, player.w, player.h, false, 0);
    ctx.fillStyle = COLORS.overlay;
    ctx.fillRect(0, 0, W, H);
    ctx.fillStyle = COLORS.maroon;
    ctx.font = 'bold 22px Agbalumo, cursive';
    ctx.textAlign = 'center';
    ctx.fillText('daramel dash!', W / 2, H / 2 - 18);
    ctx.font = '15px Agbalumo, cursive';
    ctx.fillStyle = '#D8A79E';
    ctx.fillText('press space or tap to start', W / 2, H / 2 + 12);
    ctx.textAlign = 'left';
  }

  function drawOver() {
    ctx.fillStyle = COLORS.overlay;
    ctx.fillRect(0, 0, W, H);
    ctx.fillStyle = COLORS.maroon;
    ctx.font = 'bold 20px Agbalumo, cursive';
    ctx.textAlign = 'center';
    // ctx.fillText('too much sugar!', W / 2, H / 2 - 20);
    ctx.font = '14px Agbalumo, cursive';
    ctx.fillStyle = '#D8A79E';
    ctx.fillText('score: ' + score + '   best: ' + best, W / 2, H / 2 + 8);
    ctx.fillStyle = COLORS.maroon;
    ctx.fillText('tap or space to try again', W / 2, H / 2 + 32);
    ctx.textAlign = 'left';
  }

  function loop() {
    animFrame = requestAnimationFrame(loop);
    ctx.fillStyle = COLORS.bg;
    ctx.fillRect(0, 0, W, H);
    drawGround();

    if (state === 'idle') { drawIdle(); return; }

    frameCount++;
    player.frameTick++;
    if (player.frameTick % 6 === 0) player.frame++;

    player.vy += 0.6;
    player.y += player.vy;

    if (player.y >= GROUND) {
      player.y = GROUND;
      player.vy = 0;
      player.jumps = 0;
      player.onGround = true;
    }

    spawnTimer--;
    if (spawnTimer <= 0) {
      if (state === 'running') spawnObstacle();
      spawnTimer = Math.floor(60 + Math.random() * 60);
    }

    speed = 4 + Math.floor(score / 300) * 0.5;

    for (let i = obstacles.length - 1; i >= 0; i--) {
      obstacles[i].x -= speed;
      drawBulbul(obstacles[i]);
      if (obstacles[i].x + obstacles[i].w < 0) {
        obstacles.splice(i, 1);
        continue;
      }
      if (collides(
        { x: player.x, y: player.y, w: player.w, h: player.h },
        obstacles[i]
      )) {
        state = 'over';
        if (score > best) { best = score; bestEl.textContent = best; }
      }
    }

    drawDate(player.x, player.y, player.w, player.h, player.onGround, player.frame);

    if (state === 'running') {
      score++;
      scoreEl.textContent = score;
    }

    if (state === 'over') drawOver();
  }

  initGame();
  animFrame = requestAnimationFrame(loop);
})();
</script>


<main>

<!-- <p class="description-left">
Introducing...
</p> -->

<!-- <img src="/images/logo.png" alt="Logo"> -->

<!-- <img src="/images/jar.png" alt="Jar" class="jar-logo"> -->


<h1 style="margin-top: 8rem;">DARAMEL</h1>


<p class="description">

</p>



<form id="signup-form" action="https://script.google.com/macros/s/AKfycbz7DQ2WuMorzx72GKAy6a-DOev-EVsGjvBazkYVM4vR9zCEZVCfNlbYzp87u8ql8nzm/exec" method="POST" target="hidden_iframe">
  <input type="email" name="email" placeholder="Enter your email" required>
  <button type="submit">Join the VIP Launch List</button>
</form>

<iframe name="hidden_iframe" style="display:none;"></iframe>

<p id="response"></p>

<script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>
<script>
const form = document.getElementById('signup-form');
const responseEl = document.getElementById('response');


form.addEventListener('submit', (e) => {
  // Wait a tiny bit for the form to submit
  setTimeout(() => {
    responseEl.textContent = "🎉 Thanks! You're on the list!";
    confetti({ particleCount: 150, spread: 100, origin: { y: 0.6 } });
    form.reset();
  }, 100);
});
</script>

<div class="behold-wrap" style="width:100%; margin: 2rem 0;">
  <div data-behold-id="EvcDJTv3YsLUrbOq5DH4"></div>
  <script>
    (function() {
      const d=document,s=d.createElement("script");s.type="module";
      s.src="https://w.behold.so/widget.js";d.head.append(s);
    })();
  </script>
</div>

<div class="social-follow" style="margin-bottom: 8rem">
  <p>follow along!</p>
  <a href="https://www.tiktok.com/@eatdaramel" target="_blank" aria-label="TikTok">
    <i class="fab fa-tiktok social-icon"></i>
  </a>
  <a href="https://www.instagram.com/eatdaramel/" target="_blank" aria-label="Instagram">
    <i class="fab fa-instagram social-icon"></i>
  </a>
</div>



<!-- Falling date logos -->

<script>
const logoImages = [
  "/images/logo.png"
  // "/images/dara1.png",
  // "/images/dara2.png",
  // "/images/dara3.png",
  // "/images/dara4.png",
  // "/images/dara5.png",
  // "/images/dara6.png",
  // "/images/dara7.png",
  // "/images/dara8.png",
  // "/images/dara9.png",
  // "/images/dara10.png",
  // "/images/dara11.png",
  // "/images/dara12.png",
];
</script>


<canvas id="logo-canvas" style="position:fixed; top:0; left:0; pointer-events:none; z-index:9999;"></canvas>

<script>
const canvas = document.getElementById("logo-canvas");
const ctx = canvas.getContext("2d");
canvas.width = window.innerWidth;
canvas.height = window.innerHeight;

const logos = [];
const totalLogos = 30; // number of logos falling at once

// Preload images
const loadedImages = logoImages.map(src => {
  const img = new Image();
  img.src = src;
  return img;
});

// Initialize falling logos
for (let i = 0; i < totalLogos; i++) {
  logos.push({
    x: Math.random() * canvas.width,
    y: Math.random() * canvas.height,
    speed: 1 + Math.random() * 1.5,
    size: 30 + Math.random() * 30,
    img: loadedImages[Math.floor(Math.random() * loadedImages.length)]
  });
}

function animate() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  logos.forEach(logo => {
    ctx.drawImage(logo.img, logo.x, logo.y, logo.size, logo.size);
    const isMobile = window.innerWidth <= 480;
    const speedFactor = isMobile ? 0.8 : 1;
    logo.y += logo.speed * speedFactor;
    logo.x += Math.sin(logo.y / 50); // optional slight side-to-side drift

    if (logo.y > canvas.height) {
      logo.y = -logo.size; // reset to top
      logo.x = Math.random() * canvas.width;
      logo.img = loadedImages[Math.floor(Math.random() * loadedImages.length)]; // pick a new random logo
    }
  });

  requestAnimationFrame(animate);
}

// Start animation once images are loaded
Promise.all(loadedImages.map(img => {
  return new Promise(resolve => {
    img.onload = resolve;
  });
})).then(() => animate());

// Resize canvas on window resize
window.addEventListener("resize", () => {
  canvas.width = window.innerWidth;
  canvas.height = window.innerHeight;
});
</script>

</main>