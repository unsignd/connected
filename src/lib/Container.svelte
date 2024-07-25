<script lang="ts">
  import { onMount } from 'svelte';
  import Canvas from './Canvas.svelte';

  let tabId: number;
  let data: {
    [tabId: string]: {
      window: {
        x: number,
        y: number,
      },
      point: {
        x: number,
        y: number,
      }
    }
  } = {};
  let points: {
    x: number,
    y: number,
  }[] = [];

  let interval: NodeJS.Timer;

  onMount(() => {
    tabId = sessionStorage.tabID && 
            sessionStorage.closedLastTab !== '2' ? 
            sessionStorage.tabID : 
            sessionStorage.tabID = Math.random();

    sessionStorage.closedLastTab = '2';

    update();

    interval = setInterval(() => {
      update();

      points = Object.keys(data).filter(key => key !== tabId.toString()).map((key) => ({
        x: data[key].point.x + data[key].window.x,
        y: data[key].point.y + data[key].window.y,
      }));
    }, 50);
  });

  const update = () => {
    const {x, y} = document.getElementsByClassName('point')[0].getBoundingClientRect();
    load();

    if (data) {
      localStorage.setItem('data', JSON.stringify({
        ...data,
        [tabId]: {
          window: {
            x: window.screenX,
            y: window.screenY,
            width: window.innerWidth,
            height: window.innerHeight,
          },
          point: {
            x: x + 16,
            y: y + 16,
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
            y,
          }
        }
      }));
    }

    load();
  }

  const load = () => {
    data = JSON.parse(localStorage.getItem('data') ?? '{}');
  }

  const unload = () => {
    sessionStorage.closedLastTab = '1';

    const data = JSON.parse(localStorage.getItem('data') ?? '{}');

    if (data) {
      localStorage.setItem('data', JSON.stringify({
        ...Object.keys(data).filter(key => key !== tabId.toString()).reduce((acc: {
          [key: string]: object;
        }, key) => {
            acc[key] = data[key];
            return acc;
        }, {})
      }));
    }

    if (interval) {
      clearInterval(interval);
    }
  }
</script>

<svelte:window 
  on:resize={update}
  on:beforeunload={unload}
/>

<div class="container">
  <button class="point">{Object.keys(data).findIndex(key => key === tabId.toString())}</button>
  {#if tabId && data}
  <Canvas tabId={tabId} data={data} points={points}/>
  {/if}
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