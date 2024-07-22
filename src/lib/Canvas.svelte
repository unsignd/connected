<script lang="ts">
	import { onMount } from 'svelte';

  let canvas: HTMLCanvasElement;
  let context: CanvasRenderingContext2D;

  let t: number,
      l: number,
      w: number,
      h: number;
  
  let hover: boolean;

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

    context.fillStyle = hover ? "#000" : "transparent";
    context.lineWidth = 8;

    context.clearRect(0, 0, w * 4, h * 4);
    context.beginPath();
    context.arc(w * 2, h * 2, 64 - 16, 0, 2 * Math.PI);
    context.fill();
    context.stroke();
	};

  const onMouseMove = (event: MouseEvent) => {
    const x = event.clientX * 4;
    const y = event.clientY * 4;

    hover = context.isPointInPath(x, y);

    canvas.style.cursor = hover ? 'pointer' : 'auto';

    handleSize();
  }
</script>

<svelte:window on:resize={handleSize} on:mousemove={onMouseMove}/>

<canvas bind:this={canvas} />
