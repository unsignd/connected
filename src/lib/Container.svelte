<script lang="ts">
  import { onMount } from 'svelte';
  import Canvas from './Canvas.svelte';

  let tabId: number;
  let data: {
    [tabId: string]: {
      window: {
        x: number,
        y: number,
        width: number,
        height: number,
      },
      point: {
        x: number,
        y: number,
      },
    }
  } = {};
  let points: {
    [tabId: string]: {
      x: number,
      y: number,
    },
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
      points = Object.keys(data)
      .reduce((acc, key) => {
        acc[key] = {
          x: data[key].point.x + data[key].window.x,
          y: data[key].point.y + data[key].window.y,
        };
        return acc;
      }, {} as { [key: string]: { x: number, y: number } });

      update();
    }, 0);
  });

  const update = () => {
    const {x, y} = document.getElementsByClassName('point')[0].getBoundingClientRect();
    const prevData = data;

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
    } else if (!prevData) {
      window.close();
    } else {
      alert('aa')

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
        x: Math.max(16, Math.min(data[tabId].window.width - 16, event.clientX)),
        y: Math.max(16, Math.min(data[tabId].window.height - 16, event.clientY)),
      }
    }
  }

  const mouseDown = (event: MouseEvent) => {
    if (event.clientX >= data[tabId].point.x - 16
      && event.clientX <= data[tabId].point.x + 16
      && event.clientY >= data[tabId].point.y - 16
      && event.clientY < data[tabId].point.y + 16
    ) {
      isDragging = true;
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
  on:mousedown={mouseDown}
  on:mousemove={drag}
  on:mouseup={() => isDragging = false}
/>

<div class="container">
  <button
    class="point"
    style="--x: {dragPosition.x ? dragPosition.x - 16 + 'px' : 'calc(50% - 16px)'}; --y: {dragPosition.y ? dragPosition.y - 16 + 'px' : 'calc(50% - 16px)'}"
  >
    {Object.keys(data).findIndex((key) => key === tabId.toString())}
  </button>
  {#each Object.keys(points).filter(key => key !== tabId.toString()).filter(key => points[key].x >= data[tabId].window.x - 16 && points[key].x < data[tabId].window.x + data[tabId].window.width + 16 && points[key].y >= data[tabId].window.y - 16 && points[key].y < data[tabId].window.y + data[tabId].window.height + 16) as pointKey
  }
    <div
      class="other"
      style="--x: {points[pointKey].x - data[tabId].window.x}px; --y: {points[pointKey].y - data[tabId].window.y}px;"
    >
      {Object.keys(data).findIndex((key) => key === pointKey.toString())}
    </div>
  {/each}
  {#if tabId && data}
    <Canvas bind:tabId={tabId} bind:data={data} bind:points={points}/>
  {/if}
</div>

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

    position: absolute;
    top: var(--y);
    left: var(--x);

    border-style: solid;

    z-index: 1;
  }

  .container > .other {
    width: 32px;
    height: 32px;

    position: absolute;
    top: var(--y);
    left: var(--x);

    display: flex;
    align-items: center;
    justify-content: center;

    font-weight: 600;

    color: #ccc;
    background-color: #fff;

    border: 2px dashed #ccc;

    transform: translate(-50%, -50%);
  }
</style>