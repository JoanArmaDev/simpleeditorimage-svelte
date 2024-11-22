<script lang="ts">
	import { draggable } from '@neodrag/svelte';
	import type { DragEventData, DragOptions } from '@neodrag/svelte';

	export let options: DragOptions = {
		axis: 'both'
	};

	export let text: string = 'Tu texto aquí';
	export let color: string;
	export let fontSize: number;
	export let onPositionChange: (x: number, y: number) => void;

	function handleDrag(e: CustomEvent<DragEventData>) {
		const { offsetX, offsetY } = e.detail;
		onPositionChange(offsetX, offsetY);
	}
</script>

<div
	aria-label="Texto movible"
	use:draggable={options}
	on:neodrag={handleDrag}
	class="absolute z-50 cursor-move"
	style:color
	style:font-size={`${fontSize}px`}
>
	{text}
</div>
