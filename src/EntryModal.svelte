<script lang="ts">
  import { createEventDispatcher, onMount } from 'svelte'
  import QRScanner from 'qr-scanner'
  QRScanner.WORKER_PATH = '/assets/js/qr/qr-scanner-worker.min.js'

  let qrScanner: QRScanner

  const dispatch = createEventDispatcher()
  const close = () => dispatch('close')
  const add = (entry: any) => dispatch('add', entry)
  
  let url: string
  let name: string
  const onChange: (name: any) => void = () => {}

  function parseQR(link: string) {
    if (link.startsWith('https://temperaturepass.ndi-api.gov.sg/') || link.startsWith('https://safeentry-qr.gov.sg/')) {
      const resolvedLink = link.replace('temperaturepass.ndi-api.gov.sg', 'www.safeentry-qr.gov.sg')
      const id = /login\/(.*)$/.exec(resolvedLink)[1]
      const idWithoutPrefix = id.replace(/^[^-]+-/, '').replace(/-SE$/, '')

      url = resolvedLink
      name = idWithoutPrefix
    }
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
  
  function _onAdd() {
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
    font-size: 1.5rem;
    text-align: center;
  }
  
  input {
    width: 95%;
  }
  
  input[type=url] {
    width: 100%;
  }

  .buttons {
    display: flex;
    float: right;
  }

  .cancel {
    color: rgb(0,150,200);
    cursor: pointer;
    line-height: 32px;
    margin-right: 16px;
  }
</style>

<div class="modal-background" on:click={_onCancel}></div>

<div class="modal" role="dialog" aria-modal="true">

  <h2>Add SafeEntry QR</h2>

  <!-- svelte-ignore a11y-media-has-caption -->
  <video id="qr-cam"></video>

  <div class="field">
    <input
      disabled
      type="url"
      name="url"
      bind:value={url}
      on:keydown={e => e.which === 13 && _onAdd()} />
  </div>
  <div class="field">
    <label for="name">Name: </label>
    <input
      type="text"
      name="name"
      bind:value={name}
      on:keydown={e => e.which === 13 && _onAdd()} />
  </div>
  <div class="buttons">
    <span class="cancel" on:click={_onCancel}>
      Cancel
    </span>
    <button on:click={_onAdd}>
      Add
    </button>
  </div>
</div>
