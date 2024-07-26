<script lang="ts">
  import Container from './lib/Container.svelte';
</script>

<main>
  <header class="header">
    <p class="title">./connected</p>
    <div class="group">
      <button on:click={() => window.open(document.URL, '_blank')}>add new point</button>
      <button on:click={() => localStorage.removeItem('data')}>clear data</button>
    </div>
  </header>
  <Container />
</main>

<style>
  main {
    padding: 0 16px;
  }

  .header {
    display: flex;
    align-items: center;
    justify-content: space-between;
  }

  .header > .title {
    font-weight: 600;
  }

  .header > .group {
    display: flex;
    gap: 4px;
    flex-shrink: 0;
  }
</style>
