<script lang="ts">
	import type { Text } from 'slate';
	import type { Snippet } from 'svelte';

	interface Props {
		// svelte-ignore unused-export-let
		leaf: Text;
		children?: Snippet;
	}

	let { children }: Props = $props();
</script>

<span data-slate-leaf="true">{@render children?.()}</span>
