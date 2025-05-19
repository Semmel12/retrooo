<script>
  import { onMount } from 'svelte';
  import { createEventDispatcher } from 'svelte';
const dispatch = createEventDispatcher();

  let canvas;
  let ctx;
  let paddleLeft = { x: 10, y: 100 };
  let paddleRight = { x: 380, y: 100 };
  let ball = { x: 200, y: 150, dx: 3, dy: 3 };
  let upPressed = false;
  let downPressed = false;
  let gameLoop;

  function draw() {
    ctx.fillStyle = 'black';
    ctx.fillRect(0, 0, 400, 300);

    ctx.fillStyle = '#0f0';
    ctx.fillRect(paddleLeft.x, paddleLeft.y, 10, 50);
    ctx.fillRect(paddleRight.x, paddleRight.y, 10, 50);

    ctx.beginPath();
    ctx.arc(ball.x, ball.y, 5, 0, Math.PI * 2);
    ctx.fillStyle = 'red';
    ctx.fill();
    ctx.closePath();
  }

  function update() {
    ball.x += ball.dx;
    ball.y += ball.dy;

    if (ball.y <= 0 || ball.y >= 300) ball.dy *= -1;

    if (
      ball.x <= paddleLeft.x + 10 &&
      ball.y > paddleLeft.y &&
      ball.y < paddleLeft.y + 50
    ) ball.dx *= -1;

    if (
      ball.x >= paddleRight.x - 5 &&
      ball.y > paddleRight.y &&
      ball.y < paddleRight.y + 50
    ) ball.dx *= -1;

    if (ball.x <= 0 || ball.x >= 400) {
      ball = { x: 200, y: 150, dx: 3, dy: 3 };
    }

    if (upPressed && paddleRight.y > 0) paddleRight.y -= 5;
    if (downPressed && paddleRight.y < 250) paddleRight.y += 5;

    if (ball.y < paddleLeft.y + 25) paddleLeft.y -= 3;
    if (ball.y > paddleLeft.y + 25) paddleLeft.y += 3;
  }

  function loop() {
    update();
    draw();
  }

  function keyDown(e) {
    if (e.key === 'ArrowUp') upPressed = true;
    if (e.key === 'ArrowDown') downPressed = true;
  }

  function keyUp(e) {
    if (e.key === 'ArrowUp') upPressed = false;
    if (e.key === 'ArrowDown') downPressed = false;
  }

  onMount(() => {
    ctx = canvas.getContext('2d');
    gameLoop = setInterval(loop, 1000 / 60);
    window.addEventListener('keydown', keyDown);
    window.addEventListener('keyup', keyUp);
    return () => {
      clearInterval(gameLoop);
      window.removeEventListener('keydown', keyDown);
      window.removeEventListener('keyup', keyUp);
    };
  });
</script>

<h2>🏓 Pong</h2>
<canvas bind:this={canvas} width="400" height="300"></canvas>
<br />
<button on:click={() => dispatch('exit')}>🔙 Zurück</button>
