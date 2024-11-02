<script lang="ts">
	import type { Ancestor, Path, Range, Text as SlateText } from 'slate';
	import { Editor } from 'slate';
	import { isDecoratorRangeListEqual } from '../utils';
	import { NODE_TO_INDEX, NODE_TO_PARENT } from '../weakMaps';
	import { getChildDecorations } from './Children.svelte';
	import { getDecorateContext, getEditor } from './Slate.svelte';
	import Text from './Text.svelte';

	interface Props {
		parent: Ancestor;
		text: SlateText;
		path: Path;
		index: number;
		isLeafBlock: boolean;
		decorations: Range[];
	}

	let { parent, text, path, index, isLeafBlock, decorations }: Props = $props();

	const decorateContext = getDecorateContext();
	const editor = getEditor();

	let currentIndex = $derived(index);
	let currentText = $derived(text);
	let currentPath = $derived(path);
	let currentParent = $derived(parent);
	let currentDecorations = $state(decorations);
	$effect(() => {
		if (!isDecoratorRangeListEqual(currentDecorations, decorations)) {
			currentDecorations = decorations;
		}
	});

	$effect(() => {
		NODE_TO_INDEX.set(currentText, currentIndex);
	});
	$effect(() => {
		NODE_TO_PARENT.set(currentText, currentParent);
	});

	let childPath = $derived(currentPath.concat(currentIndex));
	let isLast = $derived(isLeafBlock && currentIndex === currentParent.children.length - 1);
	let range = $derived(Editor.range(editor, childPath));
	let decorate = $derived($decorateContext);
	let childDecorations = $derived(
		getChildDecorations(decorate([currentText, childPath]), range, currentDecorations)
	);
</script>

<Text decorations={childDecorations} {isLast} parent={currentParent} text={currentText} />
