<script>
  import { onMount, createEventDispatcher } from 'svelte';
  const dispatch = createEventDispatcher();

  let canvas;
  let ctx;
  let snake;
  let food;
  let dx = 10;
  let dy = 0;
  let gameLoop;

  function resetGame() {
    snake = [{x: 50, y: 50}];
    dx = 10;
    dy = 0;
    placeFood();
  }

  function placeFood() {
    food = {
      x: Math.floor(Math.random() * 29) * 10,
      y: Math.floor(Math.random() * 29) * 10
    };
  }

  function draw() {
    ctx.fillStyle = 'black';
    ctx.fillRect(0, 0, 300, 300);

    ctx.fillStyle = '#0f0';
    snake.forEach(s => ctx.fillRect(s.x, s.y, 10, 10));

    ctx.fillStyle = 'red';
    ctx.fillRect(food.x, food.y, 10, 10);
  }

  function update() {
    const head = {x: snake[0].x + dx, y: snake[0].y + dy};

    if (head.x < 0 || head.y < 0 || head.x >= 300 || head.y >= 300 || snake.some(s => s.x === head.x && s.y === head.y)) {
      resetGame();
      return;
    }

    snake.unshift(head);
    if (head.x === food.x && head.y === food.y) {
      placeFood();
    } else {
      snake.pop();
    }
  }

  function loop() {
    update();
    draw();
  }

  function keyDown(e) {
    switch (e.key) {
      case 'ArrowUp': if (dy === 0) { dx = 0; dy = -10; } break;
      case 'ArrowDown': if (dy === 0) { dx = 0; dy = 10; } break;
      case 'ArrowLeft': if (dx === 0) { dx = -10; dy = 0; } break;
      case 'ArrowRight': if (dx === 0) { dx = 10; dy = 0; } break;
    }
  }

  onMount(() => {
    ctx = canvas.getContext('2d');
    resetGame();
    window.addEventListener('keydown', keyDown);
    gameLoop = setInterval(loop, 100);
    return () => {
      clearInterval(gameLoop);
      window.removeEventListener('keydown', keyDown);
    };
  });
</script>

<h2>🐍 Snake</h2>
<canvas bind:this={canvas} width="300" height="300"></canvas>
<br />
<button on:click={() => dispatch('exit')}>🔙 Zurück</button>
