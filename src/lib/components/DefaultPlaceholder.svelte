<script lang="ts">
	import type { Snippet } from 'svelte';

	interface Props {
		clientHeight?: number | undefined;
		children?: Snippet;
	}

	let { clientHeight = $bindable(undefined), children }: Props = $props();
</script>

<span bind:clientHeight data-slate-placeholder="true" contenteditable="false">
	{@render children?.()}
</span>

<style>
	span {
		position: absolute;
		pointer-events: none;
		width: 100%;
		max-width: 100%;
		display: block;
		opacity: 0.333;
		user-select: none;
		text-decoration: none;
	}
</style>
