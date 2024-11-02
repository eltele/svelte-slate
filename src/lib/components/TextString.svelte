<script lang="ts">
	import type { Text } from 'slate';

	interface Props {
		leaf: Text;
		isTrailing: boolean;
	}

	let { leaf, isTrailing }: Props = $props();

	let ref: HTMLSpanElement;

	$effect.pre(() => {
		if (ref) {
			ref.textContent = leaf.text;
		}
	});
</script>

<span bind:this={ref} data-slate-string="true"
	>{leaf.text}{#if isTrailing}{'\n'}{/if}</span
>
