<script lang="ts" module>
	import { Range } from 'slate';

	export function getChildDecorations(
		childDecorations: Range[],
		range: Range,
		decorations: Range[] = []
	): Range[] {
		for (const decoration of decorations) {
			const intersection = Range.intersection(decoration, range);

			if (intersection) {
				childDecorations.push(intersection);
			}
		}
		return childDecorations;
	}
</script>

<script lang="ts">
	import type { Ancestor, Selection } from 'slate';
	import { Element as SlateElement, Editor } from 'slate';
	import { findKey, findPath } from '../utils';
	import { getEditor } from './Slate.svelte';
	import ChildElement from './ChildElement.svelte';
	import ChildText from './ChildText.svelte';

	interface Props {
		node: Ancestor;
		decorations: Range[];
		selection: Selection;
	}

	let { node, decorations, selection = null }: Props = $props();

	const editor = getEditor();

	let currentNode = $derived(node);

	// $effect(() => {
	// 	if (currentNode !== node || node === editor) {
	// 		console.log('running 3');
	// 		currentNode = node;
	// 	}
	// });

	const path = $derived(findPath(currentNode));

	const isLeafBlock = $derived(
		SlateElement.isElement(currentNode) &&
			!editor.isInline(currentNode) &&
			Editor.hasInlines(editor, currentNode)
	);
</script>

{#each currentNode.children as child, index (findKey(child))}
	{#if SlateElement.isElement(child)}
		<ChildElement {decorations} {selection} element={child} parent={currentNode} {index} {path} />
	{:else}
		<ChildText {decorations} parent={currentNode} text={child} {index} {path} {isLeafBlock} />
	{/if}
{/each}
