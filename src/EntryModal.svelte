<script lang="ts">
  import { createEventDispatcher, onMount } from 'svelte'
	import QRScanner from 'qr-scanner'
	QRScanner.WORKER_PATH = '/assets/js/qr/qr-scanner-worker-min.js'

	let qrScanner

	const dispatch = createEventDispatcher()
	const close = () => dispatch('close')
	const add = (entry: any) => dispatch('add', entry)
	
	let url: string
	let name: string
	const onChange: (name: any) => void = () => {}

	function parseQR(link: string) {
		const resolvedLink = link.replace('temperaturepass.ndi-api.gov.sg', 'www.safeentry-qr.gov.sg')
		const id = /login\/(.*)$/.exec(resolvedLink)[1]
		url = resolvedLink
		const idWithoutPrefix = id.replace(/^[^-]+-/, '').replace(/-SE$/, '')
		name = idWithoutPrefix
	}

	onMount(() => {
		const videoElem = document.getElementById('qr-cam') as HTMLVideoElement
		qrScanner = new QRScanner(videoElem, parseQR)
		qrScanner.start()
	})

	function _onCancel() {
		qrScanner && qrScanner.destroy()
		close()
	}
	
	function _onOkay() {
		add({ url, name })
		qrScanner && qrScanner.destroy()
		close()
	}
	
	// Make url and name reactive with $: and a no-op fn
	$: onChange(url)
	$: onChange(name)
</script>

<style>
	.modal-background {
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		background: rgba(0,0,0,0.3);
	}

	.modal {
		position: absolute;
		left: 50%;
		top: 50%;
		width: calc(100vw - 4em);
		max-width: 32em;
		max-height: calc(100vh - 2em);
		overflow: auto;
		transform: translate(-50%,-50%);
		padding: 1em;
		border-radius: 0.2em;
		background: white;
	}

	video {
		width: 100%;
		height: auto;
		margin-bottom: 3px;
	}

	button {
		display: block;
	}

	.field {
		display: flex;
	}

	.field label {
		margin-top: 4px;
		margin-right: 4px;
		min-width: 10%;
	}

  h2 {
		font-size: 2rem;
		text-align: center;
	}
	
	input {
		width: 95%;
	}
	
	.buttons {
		display: flex;
		justify-content: space-between;
	}
	
	.close {
		position: absolute;
		top: -2rem;
		right: 0;
		background: black;
	}
</style>

<div class="modal-background" on:click={close}></div>

<div class="modal" role="dialog" aria-modal="true">

	<button class="close" on:click={_onCancel}>
		Close
	</button>

	<h2>Add SafeEntry QR</h2>

	<!-- svelte-ignore a11y-media-has-caption -->
	<video id="qr-cam"></video>

	<div class="field">
		<label for="url">URL: </label>
		<input
			type="text"
			name="url"
			bind:value={url}
			on:keydown={e => e.which === 13 && _onOkay()} />
	</div>
	<div class="field">
		<label for="name">Name: </label>
		<input
			type="text"
			name="name"
			bind:value={name}
			on:keydown={e => e.which === 13 && _onOkay()} />
	</div>
	<div class="buttons">
		<button on:click={_onCancel}>
			Cancel
		</button>
		<button on:click={_onOkay}>
			Okay
		</button>
	</div>
</div>
