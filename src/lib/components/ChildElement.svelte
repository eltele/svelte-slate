<script lang="ts" module>
	import { createContextKey, getFromContext } from '../utils';

	const SELECTED_CONTEXT_KEY = createContextKey<boolean>();

	export function getSelectedContext() {
		return getFromContext(SELECTED_CONTEXT_KEY);
	}
</script>

<script lang="ts">
	import { run } from 'svelte/legacy';

	import type { Ancestor, Element as SlateElement, Path, Selection } from 'slate';
	import { Range, Editor } from 'slate';
	import InternalElement from './InternalElement.svelte';
	import { NODE_TO_INDEX, NODE_TO_PARENT } from '../weakMaps';
	import { getChildDecorations } from './Children.svelte';
	import { createContext, isDecoratorRangeListEqual, isSelectionEqual } from '../utils';
	import { getDecorateContext, getEditor } from './Slate.svelte';

	interface Props {
		parent: Ancestor;
		element: SlateElement;
		path: Path;
		index: number;
		decorations: Range[];
		selection?: Selection;
	}

	let { parent, element, path, index, decorations, selection = null }: Props = $props();

	const decorateContext = getDecorateContext();
	const selectedContext = createContext(SELECTED_CONTEXT_KEY, false);
	const editor = getEditor();

	let currentIndex = $derived(index);
	let currentParent = $derived(parent);
	let currentElement = $derived(element);
	let currentPath = $derived(path);
	let currentDecorations = $state(decorations);

	$effect.pre(() => {
		if (!isDecoratorRangeListEqual(currentDecorations, decorations)) {
			currentDecorations = decorations;
		}
	});
	let currentSelection = $state(selection);
	$effect.pre(() => {
		if (!isSelectionEqual(currentSelection, selection)) {
			currentSelection = selection;
		}
	});

	$effect.pre(() => {
		NODE_TO_INDEX.set(currentElement, currentIndex);
	});
	$effect.pre(() => {
		NODE_TO_PARENT.set(currentElement, currentParent);
	});
	let childPath = $derived(currentPath.concat(currentIndex));
	let range = $derived(Editor.range(editor, childPath));
	let decorate = $derived($decorateContext);
	let childDecorations = $derived(
		getChildDecorations(decorate([currentElement, childPath]), range, currentDecorations)
	);
	let childSelection = $derived(currentSelection && Range.intersection(range, currentSelection));

	$effect.pre(() => {
		selectedContext.set(!!childSelection);
	});
</script>

<InternalElement
	decorations={childDecorations}
	element={currentElement}
	selection={childSelection}
/>
