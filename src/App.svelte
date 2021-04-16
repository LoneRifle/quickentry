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
  <button on:click={handleClick}>Add SafeEntry QR</button>
  <div class="menu-bar">
    <span class="force-right"/>
    <span class="manage-links" on:click={() => (showLinkManagement = !showLinkManagement)}>
      { showLinkManagement ? 'Continue' : 'Manage Links' }
    </span>
  </div>
  {#each links as link }
    <div class={ showLinkManagement ? 'link-management' : 'link' }>
      <a class="link-text" target="_blank" href="{ link.url }">{ link.name }</a>
      {#if showLinkManagement}
        <button class="link-remove" on:click={() => handleRemove(link)}>x</button>
      {/if}
    </div>
  {/each}

  {#if showModal}
    <EntryModal on:add={handleAdd} on:close={() => (showModal = false)}/>
  {/if}
</main>

<style>
  main {
    text-align: center;
    padding: 1em;
    max-width: 240px;
    margin: 0 auto;
  }

  .link {
    display: grid;
    grid-template-columns: 1fr;
  }

  .link-management {
    display: grid;
    grid-template-columns: 1fr 40px;
  }

  .link-text {
    padding: 1em;
  }

  .link-remove {
    cursor: pointer;
    margin-left: 3px;
    margin-bottom: 0;
  }

  .menu-bar {
    display: flex;
  }

  .force-right {
    flex: 1;
  }

  .manage-links {
    cursor: pointer;
    color: rgb(0,100,200);
  }

  @media (min-width: 640px) {
    main {
      max-width: none;
    }
  }
</style>
