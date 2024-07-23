<script lang="ts">
  import { onMount } from 'svelte';

  let tabId: number;
  let data: string;

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
    load();

    if (data) {
      localStorage.setItem('data', JSON.stringify({
        ...JSON.parse(data),
        [tabId]: {
          window: {
            x: window.screenX,
            y: window.screenY,
            width: window.innerWidth,
            height: window.innerHeight,
          },
          point: {
            x,
            y
          }
        }
      }));
    } else {
      localStorage.setItem('data', JSON.stringify({
        [tabId]: {
          window: {
            x: window.screenX,
            y: window.screenY,
            width: window.innerWidth,
            height: window.innerHeight,
          },
          point: {
            x,
            y
          }
        }
      }));
    }

    load();
  }

  const load = () => {
    data = localStorage.getItem('data') ?? '{}';
  }

  const unload = () => {
    sessionStorage.closedLastTab = '1';

    const data = localStorage.getItem('data') ?? '{}';

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
  <button class="point">{Object.keys(JSON.parse(data ?? '{}')).findIndex(key => key === tabId.toString())}</button>
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