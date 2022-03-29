<script lang=ts>
	import { onMount } from "svelte";

	let video : HTMLVideoElement
	let canvas : HTMLCanvasElement
	
	
	const constraints = {
	  audio: false,
	  video: true
	};

	onMount(() => {
		navigator.mediaDevices.getUserMedia(constraints).then(handleSuccess).catch(handleError);
	})

	function handleSuccess(stream) {
	//   (window as any).stream = stream; // make stream available to browser console
	  video.srcObject = stream;
	}
	
	function handleError(error) {
	  console.log('navigator.MediaDevices.getUserMedia error: ', error.message, error.name);
	}
	
	let x = 0
	function scan() {
		if (x < canvas.height)
		{
			x++
			canvas.getContext('2d').drawImage(video, 
				0, x, canvas.width, 1, 0, x, canvas.width, 1);
			requestAnimationFrame(scan)
		}
		else
		{
			x = 0
		}
	}
</script>

<main>
    <!-- svelte-ignore a11y-media-has-caption -->
    <video bind:this={video} playsinline autoplay></video>
	<canvas bind:this={canvas}></canvas>
	<button on:click={() => {
		canvas.width = video.videoWidth;
		canvas.height = video.videoHeight;	  
		// canvas.style.borderTop = '2px solid cyan'
		// canvas.style.boxShadow = '10px 0 20px -2px #0ff'
		scan()
	  }}> FREEZE </button>
</main>

<style>
	canvas, video {
		display: block;
		position: absolute;
		width: 100vw;
		height: auto;
		background: transparent;
	}
	video, canvas { 
		bottom: 50%;
	}
	button {
		position: absolute;
		top: 0;
		left: 0;
		z-index: 9;
	}
</style>