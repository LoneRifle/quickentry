<script lang="ts">
import EntryModal from './EntryModal.svelte'

let showModal = false
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
	{#each links as link }
		<div class="link">
			<a target="_blank" href="{ link.url }">{ link.name }</a>
			<button class="link-remove" on:click={() => handleRemove(link)}>x</button>
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

	.link-remove {
		cursor: pointer;
		margin-left: 3px;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>
