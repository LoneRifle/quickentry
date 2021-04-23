<script lang="ts">
import EntryModal from './EntryModal.svelte'

let showModal = false
let showLinkManagement = false
let links: { name: string, url: string, ts: number }[] = JSON.parse(window.localStorage.getItem('links')) || []

function handleClick() {
  showModal = true
}
function save(newLinks: { name: string, url: string, ts: number }[]) {
  window.localStorage.setItem('links', JSON.stringify(newLinks))
  links = newLinks
}
function handleAdd({ detail }: { detail: { name: string, url: string } }) {
  save([{ ...detail, ts: Date.now()  }, ...links])
}
function handleRemove({ name, url }: { name: string, url: string }) {
  save(links.filter(l => l.name !== name && l.url !== url))
}
</script>

<main>
  <section class="menu-bar">
    <div class="menu-logo">
      <img alt="favicon" src="/favicon.svg" />
      <span class="text">QuickEntry</span>
    </div>
    <button on:click={handleClick}>Add SafeEntry QR</button>
  </section>
  <section class="links-container">
    <div class="links-options">
      <div class="links-title">Saved Places</div>
      <div class="manage-links" on:click={() => (showLinkManagement = !showLinkManagement)}>
        { showLinkManagement ? 'Done' : 'Manage' }
      </div>
    </div>
    <div class="links">
      {#each links as link }
          <div class={ showLinkManagement ? 'link-management' : 'link' }>
            <a target="_blank" href="{ link.url }">
              <img alt="logo" src="/assets/images/map-marker-outline.svg" />
              <div class="link-text">{ link.name }</div>
            </a>
            {#if showLinkManagement}
                <button class="link-remove" on:click={() => handleRemove(link)}>x</button>
            {/if}
          </div>
      {/each}
    </div>
  </section>
  {#if showModal}
    <EntryModal on:add={handleAdd} on:close={() => (showModal = false)}/>
  {/if}
</main>
<footer>
  Made quickly by 
  <a target="_blank" href="https://github.com/LoneRifle">@LoneRifle</a> 
  and <a target="_blank" href="https://github.com/tatatee">@tatatee</a>
</footer>
<style>
  main {
    text-align: center;
    padding: 1em;
    max-width: none;
    min-height: 91vh;
  }

  footer {
    text-align: center;
    font-size: small;
    color: #9B9A9C;
  }

  footer a {
    color: #9B9A9C;
  }

  .menu-bar {
    display: flex;
    justify-content: space-between;
  }

  .menu-logo {
    display: inline-block;
  }

  .menu-logo > img {
    height: 32px;
    width: 32px;
  }

  .menu-logo > .text {
    vertical-align: super;
    font-size: 24px;
  }

  .links-container {
    display: block;
  }

  .links-title {
    font-weight: bolder;
  }

  .links-options {
    display: flex;
    justify-content: space-between;
    margin: 1.5em 0;
  }

  .manage-links {
    cursor: pointer;
    color: rgb(0,100,200);
  }

  .link {
    display: grid;
    grid-template-columns: 1fr;
    padding: 0 1em;
    text-align: left;
  }

  .link-management {
    display: grid;
    grid-template-columns: 1fr 40px;
    padding: 0 1em;
    text-align: left;
  }


  .link a, .link-management a {
    display: flex;
  }

  .link-text {
    padding: 1em;
  }

  .link-remove {
    cursor: pointer;
    margin-left: 3px;
    margin-bottom: 0;
  }
</style>
