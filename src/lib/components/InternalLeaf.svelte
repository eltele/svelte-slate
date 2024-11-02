<script lang="ts" module>
	export interface ILeafProps extends HTMLAttributes<HTMLElement> {
		leaf: SlateText;
	}

	export interface IPlaceholderProps extends HTMLAttributes<HTMLElement> {
		clientHeight?: number;
	}
</script>

<script lang="ts">
	import { run } from 'svelte/legacy';

	import { CAN_USE_DOM } from '../environment';
	import { PLACEHOLDER_SYMBOL } from '../weakMaps';
	import type { Text as SlateText, Element as SlateElement } from 'slate';
	import { onDestroy } from 'svelte';
	import String from './String.svelte';
	import { getFromContext } from '../utils';
	import { LEAF_CONTEXT_KEY, PLACEHOLDER_CONTEXT_KEY } from './Editable.svelte';
	import type { HTMLAttributes } from 'svelte/elements';

	interface Props {
		isLast: boolean;
		leaf: SlateText & { placeholder?: string };
		parent: SlateElement;
		text: SlateText;
	}

	let { isLast, leaf, parent, text }: Props = $props();

	const LeafContext = getFromContext(LEAF_CONTEXT_KEY);
	const PlaceholderContext = getFromContext(PLACEHOLDER_CONTEXT_KEY);

	let Leaf = $derived($LeafContext);
	let Placeholder = $derived($PlaceholderContext);

	let clientHeight: number | undefined = $state();
	let currentClientHeight: number | undefined = $state();

	$effect.pre(() => {
		if (currentClientHeight !== clientHeight && CAN_USE_DOM) {
			const editorEl = document.querySelector<HTMLDivElement>('[data-svelte-editor="true"]');

			if (editorEl) {
				editorEl.style.minHeight = `${clientHeight}px`;
			}
			currentClientHeight = clientHeight;
		}
	});

	onDestroy(() => {
		if (CAN_USE_DOM) {
			const editorEl = document.querySelector<HTMLDivElement>('[data-svelte-editor="true"]');

			if (editorEl) {
				editorEl.style.minHeight = 'auto';
			}
		}
	});
</script>

<Leaf {leaf}>
	{#if PLACEHOLDER_SYMBOL in leaf}
		<Placeholder bind:clientHeight>{leaf.placeholder}</Placeholder>
	{/if}
	<String {isLast} {leaf} {parent} {text} />
</Leaf>
