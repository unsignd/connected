<script>
  import { onMount } from 'svelte';

  onMount(() => {
    update();
  });

  const update = () => {
    const {x, y} = document.getElementsByClassName('point')[0].getBoundingClientRect();

    window.localStorage.setItem('data', JSON.stringify({
      windows: [{
        x,
        y
      }]
    }));
  }
</script>

<svelte:window 
  on:resize={update}
/>

<div class="container">
  <button class="point">1</button>
</div>

<style>
  .container {
    width: 100vw;
    height: 100vh;

    position: absolute;
    top: 0;
    left: 0;

    display: flex;
    align-items: center;
    justify-content: center;

    z-index: -1;
  }

  .container > .point {
    width: 32px;
    height: 32px;
  }
</style>