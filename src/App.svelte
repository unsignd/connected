<script lang="ts">
  import Container from './lib/Container.svelte';
</script>

<main>
  <header class="header">
    <div class="logo">
      <a class="github" href="https://github.com/unsignd/connected" target="_blank">github repo by any chance?</a>
      <p class="title">./connected</p>
    </div>
    <div class="group">
      <button on:click={() => window.open(document.URL, '_blank')}>add new point</button>
      <button on:click={() => localStorage.removeItem('data')}>clear data</button>
    </div>
  </header>
  <Container />
</main>

<style>
  main {
    padding: 16px;
  }

  .header {
    display: flex;
    align-items: center;
    justify-content: space-between;
  }

  .header > .logo {
    width: 250px;
    height: 32px;

    display: flex;
    align-items: center;

    position: relative;

    overflow: hidden;
  }

  .header > .logo > .title {
    position: absolute;
    
    font-weight: 600;

    transition: margin 150ms ease-in-out;
  }

  .header > .logo > .github {
    position: absolute;
    margin-top: -64px;

    font-weight: 600;
    text-decoration: underline;
    text-decoration-thickness: 2px;

    transition: margin 150ms ease-in-out;
    cursor: pointer;
  }

  .header > .logo:hover > .title {
    margin-top: 64px;
  }

  .header > .logo:hover > .github {
    margin-top: 0;
  }

  .header > .group {
    display: flex;
    gap: 4px;
    flex-shrink: 0;
  }
</style>
