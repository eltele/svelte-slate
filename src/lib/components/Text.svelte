<script lang="ts">
	import type { Element, Range } from 'slate';
	import { Text as SlateText } from 'slate';
	import { findKey } from '../utils';
	import { EDITOR_TO_KEY_TO_ELEMENT, ELEMENT_TO_NODE, NODE_TO_ELEMENT } from '../weakMaps';
	import InternalLeaf from './InternalLeaf.svelte';
	import { getEditor } from './Slate.svelte';

	interface Props {
		decorations: Range[];
		isLast: boolean;
		parent: Element;
		text: SlateText;
	}

	let { decorations, isLast, parent, text }: Props = $props();

	const editor = getEditor();

	let leaves = $derived(SlateText.decorations(text, decorations));
	let key = $derived(findKey(text));

	let ref: HTMLSpanElement | undefined = $state();
	$effect.pre(() => {
		if (ref) {
			EDITOR_TO_KEY_TO_ELEMENT.get(editor)?.set(key, ref);
			NODE_TO_ELEMENT.set(text, ref);
			ELEMENT_TO_NODE.set(ref, text);
		}
	});
</script>

<span bind:this={ref} data-slate-node="text"
	>{#each leaves as leaf, index (`${key}-${index}`)}<InternalLeaf
			isLast={isLast && index === leaves.length - 1}
			{parent}
			{leaf}
			{text}
		/>{/each}</span
>
