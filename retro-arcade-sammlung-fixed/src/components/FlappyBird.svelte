<script>
  import { onMount } from 'svelte';
  import { createEventDispatcher } from 'svelte';
const dispatch = createEventDispatcher();

  let canvas;
  let ctx;
  let bird = { x: 60, y: 150, dy: 0 };
  let pipes = [];
  let score = 0;
  let gameLoop;
  const gravity = 0.5;
  const lift = -8;

  function draw() {
    ctx.fillStyle = 'black';
    ctx.fillRect(0, 0, 300, 300);

    ctx.fillStyle = 'yellow';
    ctx.beginPath();
    ctx.arc(bird.x, bird.y, 10, 0, Math.PI * 2);
    ctx.fill();

    ctx.fillStyle = 'green';
    pipes.forEach(p => {
      ctx.fillRect(p.x, 0, 30, p.top);
      ctx.fillRect(p.x, p.top + 80, 30, 300 - p.top - 80);
    });

    ctx.fillStyle = '#0f0';
    ctx.fillText('Score: ' + score, 10, 20);
  }

  function update() {
    bird.dy += gravity;
    bird.y += bird.dy;

    if (bird.y < 0 || bird.y > 300) reset();

    pipes.forEach(p => {
      p.x -= 2;
      if (
        bird.x > p.x && bird.x < p.x + 30 &&
        (bird.y < p.top || bird.y > p.top + 80)
      ) reset();

      if (p.x + 30 === bird.x) score++;
    });

    if (pipes[0] && pipes[0].x < -30) pipes.shift();

    if (pipes.length === 0 || pipes[pipes.length - 1].x < 150) {
      const top = Math.random() * 150 + 20;
      pipes.push({ x: 300, top });
    }
  }

  function reset() {
    bird = { x: 60, y: 150, dy: 0 };
    pipes = [];
    score = 0;
  }

  function flap() {
    bird.dy = lift;
  }

  onMount(() => {
    ctx = canvas.getContext('2d');
    reset();
    window.addEventListener('keydown', flap);
    gameLoop = setInterval(() => {
      update();
      draw();
    }, 1000 / 60);
    return () => {
      clearInterval(gameLoop);
      window.removeEventListener('keydown', flap);
    };
  });
</script>

<h2>🕊️ Flappy Bird</h2>
<canvas bind:this={canvas} width="300" height="300"></canvas>
<br />
<button on:click={() => dispatch('exit')}>🔙 Zurück</button>
