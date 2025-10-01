+++
title = "Daramel"
+++


<main>

<p class="description-left">
Introducing...
</p>

<img src="/images/logo.png" alt="Logo">

# Daramel


<p class="description">
dates are a fruit known as “nature’s caramel”. daramel celebrates the delicious & nutritious characteristics of dates - no added sugar needed! daramel is the perfect addition to ice cream, yogurt, coffee, toast, pancakes, or straight from a spoon!
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
    speed: 1 + Math.random() * 3,
    size: 30 + Math.random() * 30,
    img: loadedImages[Math.floor(Math.random() * loadedImages.length)]
  });
}

function animate() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  logos.forEach(logo => {
    ctx.drawImage(logo.img, logo.x, logo.y, logo.size, logo.size);
    logo.y += logo.speed;
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