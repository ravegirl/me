<script lang="ts">
	let { children } = $props();
</script>

<svelte:head>
	<link rel="icon" href="/pfp.png" />
	<link rel="stylesheet" href="/css/page.css" />
	<link rel="stylesheet" href="/css/modal.css" />
</svelte:head>

{@render children()}
