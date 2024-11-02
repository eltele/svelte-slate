<script lang="ts">
	import type { Element } from 'slate';

	interface Props {
		// svelte-ignore unused-export-let
		element: Element;
		isInline: boolean;
		isVoid: boolean;
		contenteditable: boolean | undefined | null;
		ref?: HTMLElement | undefined;
		dir?: 'rtl' | 'ltr' | undefined;
		children?: import('svelte').Snippet;
	}

	let {
		element,
		isInline,
		isVoid,
		contenteditable,
		ref = $bindable(undefined),
		dir = undefined,
		children
	}: Props = $props();
</script>

{#if isInline}
	<span
		bind:this={ref}
		data-slate-node="element"
		data-slate-inline={isInline}
		data-slate-void={isVoid}
		{dir}
		{contenteditable}
	>
		{@render children?.()}
	</span>
{:else}
	<div bind:this={ref} data-slate-node="element" data-slate-void={isVoid} {dir} {contenteditable}>
		{@render children?.()}
	</div>
{/if}

<style>
	span,
	div {
		position: relative;
	}
</style>
