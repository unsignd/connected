<script lang="ts">
  import { onMount } from 'svelte';

  let tabId: number;

  onMount(() => {
    tabId = sessionStorage.tabID && 
            sessionStorage.closedLastTab !== '2' ? 
            sessionStorage.tabID : 
            sessionStorage.tabID = Math.random();

    sessionStorage.closedLastTab = '2';

    update();
  });

  const update = () => {
    const {x, y} = document.getElementsByClassName('point')[0].getBoundingClientRect();
    const data = localStorage.getItem('data');

    if (data) {
      localStorage.setItem('data', JSON.stringify({
        ...JSON.parse(data),
        [tabId]: {
          x,
          y,
        }
      }));
    } else {
      localStorage.setItem('data', JSON.stringify({
        [tabId]: {
          x,
          y,
        }
      }));
    }
  }

  const unload = () => {
    sessionStorage.closedLastTab = '1';

    const data = localStorage.getItem('data');

    if (data) {
      localStorage.setItem('data', JSON.stringify({
        ...Object.keys(JSON.parse(data)).filter(key => key !== tabId.toString()).reduce((acc: {
          [key: string]: object;
        }, key) => {
            acc[key] = JSON.parse(data)[key];
            return acc;
        }, {})
      }));
    }
  }
</script>

<svelte:window 
  on:resize={update}
  on:beforeunload={unload}
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