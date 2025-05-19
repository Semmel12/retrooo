<script>
  import SnakeGame from './components/SnakeGame.svelte';
  import PongGame from './components/PongGame.svelte';
  import FlappyBird from './components/FlappyBird.svelte';

  let current = 'menu';
</script>

<main>
  {#if current === 'menu'}
    <h1>🎮 Retro Arcade Sammlung</h1>
    <button on:click={() => current = 'snake'}>🐍 Snake</button>
    <button on:click={() => current = 'pong'}>🏓 Pong</button>
    <button on:click={() => current = 'flappy'}>🕊️ Flappy Bird</button>
  {:else if current === 'snake'}
    <SnakeGame on:exit={() => current = 'menu'} />
  {:else if current === 'pong'}
    <PongGame on:exit={() => current = 'menu'} />
  {:else if current === 'flappy'}
    <FlappyBird on:exit={() => current = 'menu'} />
  {/if}
</main>
