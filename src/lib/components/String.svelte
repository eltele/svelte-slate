<script lang="ts">
	import { findPath } from '../utils';
	import type { Text as SlateText, Element as SlateElement } from 'slate';
	import { Node, Editor, Path } from 'slate';
	import TextString from './TextString.svelte';
	import ZeroWidthString from './ZeroWidthString.svelte';
	import { getEditor } from './Slate.svelte';

	interface Props {
		isLast: boolean;
		leaf: SlateText;
		parent: SlateElement;
		text: SlateText;
	}

	let { isLast, leaf, parent, text }: Props = $props();

	const editor = getEditor();

	let isTrailing = $derived(isLast && leaf.text.slice(-1) === '\n');
	let path = $derived(findPath(text));
	let parentPath = $derived(Path.parent(path));

	let isVoid = $derived(editor.isVoid(parent));
	let isEmpty = $derived(leaf.text === '');
	let isLineBreak = $derived(
		isEmpty &&
			parent.children[parent.children.length - 1] === text &&
			!editor.isInline(parent) &&
			Editor.string(editor, parentPath) === ''
	);
</script>

{#if isVoid}
	<ZeroWidthString length={Node.string(parent).length} />
{:else if isLineBreak}
	<ZeroWidthString isLineBreak />
{:else if isEmpty}
	<ZeroWidthString />
{:else}
	<TextString {isTrailing} {leaf} />
{/if}
