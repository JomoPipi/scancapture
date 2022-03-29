<script lang=ts>

	let video : HTMLVideoElement
	let scanner : HTMLElement
	let canvas : HTMLCanvasElement
	
	
	const constraints = {
	  audio: false,
	  video: true
	};
	
	function handleSuccess(stream) {
	//   (window as any).stream = stream; // make stream available to browser console
	  video.srcObject = stream;
	}
	
	function handleError(error) {
	  console.log('navigator.MediaDevices.getUserMedia error: ', error.message, error.name);
	}
	
	navigator.mediaDevices.getUserMedia(constraints).then(handleSuccess).catch(handleError);

	function scan() {
		const h = scanner.offsetHeight
		if (h < video.offsetHeight)
		{
			scanner.style.height = Math.min(video.videoHeight, h + 1) + 'px'
			requestAnimationFrame(scan)
		}
	}
</script>

<main>
    <canvas bind:this={canvas}></canvas>
    <!-- svelte-ignore a11y-media-has-caption -->
    <video bind:this={video} playsinline autoplay></video>
	<div bind:this={scanner} class=scanner></div>
	<button on:click={() => {
		canvas.width = video.videoWidth;
		canvas.height = video.videoHeight;	  
		canvas.getContext('2d').drawImage(video, 0, 0, canvas.width, canvas.height);
		video.style.borderTop = '2px solid cyan'
		video.style.boxShadow = '10px 0 20px -2px #0ff'
		scan()
	  }}> FREEZE </button>
</main>

<style>
	canvas, video, .scanner {
		display: block;
		position: absolute;
		width: 100vw;
		height: auto;
		background: blue;
	}
	canvas { top: 0; }
	video, .scanner { 
		bottom: 0;
	}
	.scanner {
		height: 0px;
	}
	button {
		position: absolute;
		top: 0;
		left: 0;
		z-index: 9;
	}
</style>