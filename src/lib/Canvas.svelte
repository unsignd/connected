<script lang="ts">
	import { onMount } from 'svelte';

  let canvas: HTMLCanvasElement;
  let context;

  let t, l;

  onMount(() => {
		context = canvas.getContext('2d')!;
		context.lineWidth = 3;
		
		handleSize();
	});

  const handleSize = () => {
		const { top, left } = canvas.getBoundingClientRect();
		t = top;
		l = left;
	};
</script>

<canvas bind:this={canvas} />
