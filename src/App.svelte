<script lang=ts>
	import { onMount } from "svelte";

	let video : HTMLVideoElement
	let canvas : HTMLCanvasElement
	let ctx : CanvasRenderingContext2D
	
	
	const constraints = {
	  audio: false,
	  video: true
	};

	onMount(() => {
		navigator.mediaDevices.getUserMedia(constraints)
			.then(handleSuccess)
			.catch(handleError);
	})

	function handleSuccess(stream) {
	//   (window as any).stream = stream; // make stream available to browser console
	  	video.srcObject = stream;
		canvas.width = video.videoWidth
		canvas.height = video.videoHeight
		ctx = canvas.getContext('2d')
	}
	
	function handleError(error) {
	  console.log('navigator.MediaDevices.getUserMedia error: ', error.message, error.name);
	}
	
	let nowScanning = false
	let paused = false;
	function scan(dir : number) {
		if (nowScanning) return;
		nowScanning = true;
		let x = 0;
		canvas.width = video.videoWidth
		canvas.height = video.videoHeight
		const goesUpOrDown = dir >= 2
		const end = goesUpOrDown ? canvas.height : canvas.width;
		(function go() {
			if (paused)
			{
				requestAnimationFrame(go)
				return;
			}
			if (x === end) 
			{
				nowScanning = false;
				return;
			}
			
			x++
			ctx.strokeStyle = 'red'
			ctx.lineWidth = 1
			const pos = dir % 2 === 1
			const y = pos ? x : end - 1 - x;
			const trail = pos ? 1 : -1;
			if (goesUpOrDown)
			{
				ctx.strokeRect(0, y + 3 * trail, canvas.width, trail * 5)
				ctx.drawImage(video, 
					0, y, canvas.width, 1, 0, y, canvas.width, 1);
			}
			else
			{
				ctx.strokeRect(y + 3 * trail, 0, trail * 5, canvas.height)
				ctx.drawImage(video, 
					y, 0, 1, canvas.height, y, 0, 1, canvas.height);
			}
			requestAnimationFrame(go)
		})()
	}
	function reset() {
		ctx.clearRect(0, 0, canvas.width, canvas.height)
	}
	function pause() {
		paused = !paused
	}
	function drawPencil(e: PointerEvent) {
		const size = 20
		const s2 = size / 2
		const x = e.offsetX * (canvas.width / canvas.offsetWidth)
		const y = e.offsetY * (canvas.height / canvas.offsetHeight)
		console.log('x,y =',x,y)
		ctx.drawImage(video, 
			x, y, size, size, x, y, size, size);
	}
</script>

<main>
	<h3> funpics.vercel.app </h3>
	
	<div class=buttons>	
		<button on:click={() => scan(0)}> ⇦ </button>
		<button on:click={() => scan(1)}> ⇨ </button>
		<button on:click={() => scan(2)}> ⇧ </button>
		<button on:click={() => scan(3)}> ⇩ </button>
		<button on:click={pause}> pause </button>
		<button on:click={reset}> reset </button>
	</div>

	<div class=container>
		<!-- svelte-ignore a11y-media-has-caption -->
		<video bind:this={video} playsinline autoplay></video>
		<canvas bind:this={canvas}
			on:pointerdown={drawPencil}
			on:pointermove={drawPencil}>
		</canvas>
	</div>
</main>

<style>
	h3 {
		margin: 1em;
		color: #c50;
		display: block;
	}
	main {
		height: 100%;
		width: 100%;
	}
	.buttons {
		display: flex;
		justify-content: center;
	}
	.buttons > button {
		padding: 1em;
	}
	.container {
		position: relative;
		width: 100%; 
	}
	canvas, video {
		display: block;
		position: absolute;
		width: 100%;
		height: auto;
		background: transparent;
	}
	
</style>