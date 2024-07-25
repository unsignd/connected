<script lang="ts">
	import { onMount } from 'svelte';

  export let tabId: number;
  export let data: {
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
  };
  export let points: {
    [tabId: string]: {
      x: number,
      y: number,
    },
  };

  let canvas: HTMLCanvasElement;
  let context: CanvasRenderingContext2D;
  
  let t: number,
      l: number,
      w: number,
      h: number;

  let interval: NodeJS.Timer;

  onMount(() => {
		context = canvas.getContext('2d')!;
		
    handleSize();

    interval = setInterval(() => {
      clear();
      draw();
    }, 50);
	});

  const handleSize = () => {
		const { top, left, width, height } = canvas.getBoundingClientRect();
		t = top;
		l = left;
    w = width;
    h = height;
	};

  const clear = () => {
    context.clearRect(0, 0, w * 4, h * 4);
  }

  const draw = () => {
    canvas.width = w * 4;
    canvas.height = h * 4;
    context.fillStyle = "#000";
    context.lineWidth = 8;

    Object.keys(points).forEach(key => {
      context.beginPath();
      context.moveTo(data[tabId.toString()].point.x * 4, data[tabId.toString()].point.y * 4);
      context.lineTo((points[key].x - data[tabId.toString()].window.x) * 4, (points[key].y - data[tabId.toString()].window.y) * 4);
      context.stroke();
      context.closePath();
    });
  }

  const unload = () => {
    if (interval) {
      clearInterval(interval);
    }
  }
</script>

<svelte:window
  on:resize={() => {
    handleSize();
    clear();
    draw();
  }}
  on:beforeunload={unload}
/>

<canvas class="canvas" bind:this={canvas} />

<style>
  .canvas {
    width: 100%;
    height: 100%;

    position: absolute;
    top: 0;
    left: 0;

    z-index: -1;
  }
</style>