<script lang="ts">
	interface Props {
		length?: number;
		isLineBreak?: boolean;
	}

	let { length = 0, isLineBreak = false }: Props = $props();
</script>

<span data-slate-zero-width={isLineBreak ? 'n' : 'z'} data-slate-length={length}
	>{'\uFEFF'}{#if isLineBreak}<br />{/if}</span
>
