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

  onMount(() => {
		context = canvas.getContext('2d')!;
		
		handleSize();
	});

  const handleSize = () => {
		const { top, left, width, height } = canvas.getBoundingClientRect();
		t = top;
		l = left;
    w = width;
    h = height;
    canvas.width = w * 4;
    canvas.height = h * 4;
    context.fillStyle = "#000";
    context.lineWidth = 8;
    context.clearRect(0, 0, w * 4, h * 4);
    context.beginPath();
    context.moveTo(data[tabId.toString()].point.x * 4, data[tabId.toString()].point.y * 4);
    context.lineTo(0, 0);
    context.stroke();
	};
</script>

<svelte:window on:resize={handleSize}/>

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