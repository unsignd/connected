<script lang="ts">
  import Container from './lib/Container.svelte';
</script>

<main>
  <header class="header">
    <p class="title">./connected</p>
    <div class="group">
      <button>Add New Window</button>
      <button>Github</button>
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
