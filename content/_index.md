+++
title = "Daramel"
+++

<img src= '{{ "images/Logo.png" | relURL}}' alt="Logo"> 

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

<img id="thank-you-img" src="/images/wink_peace.png" alt="Thank You!" style="display:none; max-width:200px; margin-top:1rem;">