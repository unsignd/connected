<script lang="ts">
	import { onMount } from 'svelte';

  export let tabId: number;
  export let data: {
    [tabId: string]: {
      window: {
        x: number,
        y: number
      },
      point: {
        x: number,
        y: number
      }
    }
  };

  let canvas: HTMLCanvasElement;
  let context: CanvasRenderingContext2D;
  
  let t: number,
      l: number,
      w: number,
      h: number;
  let points: {
    x: number,
    y: number,
  }[] = [];

  let interval: NodeJS.Timer;

  onMount(() => {
		context = canvas.getContext('2d')!;
		
    handleSize();

    interval = setInterval(() => {
      points = Object.keys(data).filter(key => key !== tabId.toString()).map(key => {
        console.log(key);
        console.log(tabId.toString());
        console.log('-')

        return ({
          x: data[key].point.x + data[key].window.x,
          y: data[key].point.y + data[key].window.y,
        });
      })

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

    points.forEach(point => {
      context.beginPath();
      context.moveTo(data[tabId.toString()].point.x * 4, data[tabId.toString()].point.y * 4);
      context.lineTo((point.x - data[tabId.toString()].window.x) * 4, (point.y - data[tabId.toString()].window.y) * 4);
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