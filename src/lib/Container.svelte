

<div class="container">
  <button class="button">1</button>
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

  .container > .button {
    width: 32px;
    height: 32px;
  }
</style>