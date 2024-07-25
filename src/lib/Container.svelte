<script lang="ts">
  import { onMount } from 'svelte';
  import Canvas from './Canvas.svelte';

  let tabId: number;
  let data: {
    [tabId: string]: {
      window: {
        x: number,
        y: number
      },
      point: {
        x: number,
        y: number
      },
    }
  } = {};

  let isDragging: boolean = false;
  let dragPosition: {
    x?: number,
    y?: number,
  } = {
    x: undefined,
    y: undefined,
  }

  let interval: NodeJS.Timer;

  onMount(() => {
    tabId = sessionStorage.tabID && 
            sessionStorage.closedLastTab !== '2' ? 
            sessionStorage.tabID : 
            sessionStorage.tabID = Math.random();

    sessionStorage.closedLastTab = '2';

    interval = setInterval(() => {
      update();
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
            y: y + 16
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

  const drag = (event: MouseEvent) => {
    if (isDragging) {
      dragPosition = {
        x: event.clientX,
        y: event.clientY,
      }
    }
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
  on:mousemove={drag}
/>

<div class="container">
  <button class="point" on:mousedown={() => isDragging = true} on:mouseup={() => isDragging = false} style="--x: {dragPosition.x ? dragPosition.x + 'px' : '50%'}; --y: {dragPosition.y ? dragPosition.y + 'px' : '50%'}">{Object.keys(data).findIndex(key => key === tabId.toString())}</button>
  {#if tabId && data}
  <Canvas bind:tabId={tabId} bind:data={data}/>
  {/if}
</div>
{JSON.stringify(isDragging)}

<style>
  .container {
    width: 100vw;
    height: 100vh;

    position: absolute;
    top: 0;
    left: 0;

    z-index: -1;
  }

  .container > .point {
    width: 32px;
    height: 32px;

    position: relative;
    top: var(--y);
    left: var(--x);

    transform: translate(-50%, -50%);
  }
</style>