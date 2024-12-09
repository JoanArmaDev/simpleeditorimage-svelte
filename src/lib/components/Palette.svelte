<script lang="ts">
	import { createEventDispatcher } from 'svelte';
	import Button from './ui/button/button.svelte';
	const dispatch = createEventDispatcher();

	export let paletteColor = '';
	export let background = '#fff';
	export let shapeMode: '' | 'rect' | 'circle' = '';
	export let lineWidth = 5;
	export let clearCanvas = () => {};
</script>

<section>
	<div>
		<input
			class="h-[2.5rem] w-[2.5rem] cursor-pointer rounded-lg border-none bg-transparent"
			type="color"
			bind:value={paletteColor}
			on:input={() => dispatch('color', { color: paletteColor })}
			title="Selecciona un color"
		/>
	</div>
	<button title="Trazo" aria-label="Trazo" on:click={() => shapeMode = ''}>
		<svg style:color={paletteColor} viewBox="-50 -50 100 100">
			<g fill="currentColor" stroke="currentColor" stroke-width="0" stroke-linecap="round">
				<path d="M -38 12 a 38 38 0 0 0 76 0 q 0 -28 -38 -62 -38 34 -38 62" />
			</g>
		</svg>
	</button>

	<Button
		title="Rectángulo"
		variant="ghost"
		size="sm"
		on:click={() => {
			shapeMode = 'rect';

		}}
		class={shapeMode === 'rect' ? 'bg-accent' : ''}
	>
		<svg
			xmlns="http://www.w3.org/2000/svg"
			width="24"
			height="24"
			viewBox="0 0 24 24"
			fill="none"
			stroke="currentColor"
			stroke-width="2"
			stroke-linecap="round"
			stroke-linejoin="round"
			class="icon icon-tabler icons-tabler-outline icon-tabler-rectangle"
			><path stroke="none" d="M0 0h24v24H0z" fill="none" /><path
				d="M3 5m0 2a2 2 0 0 1 2 -2h14a2 2 0 0 1 2 2v10a2 2 0 0 1 -2 2h-14a2 2 0 0 1 -2 -2z"
			/></svg
		>
	</Button>
	<Button
		title="Elipse"
		variant="ghost"
		size="sm"
		on:click={() => {
			shapeMode = 'circle';
		}}
		class={shapeMode === 'circle' ? 'bg-accent' : ''}
	>
		<svg
			xmlns="http://www.w3.org/2000/svg"
			width="24"
			height="24"
			viewBox="0 0 24 24"
			fill="none"
			stroke="currentColor"
			stroke-width="2"
			stroke-linecap="round"
			stroke-linejoin="round"
			class="icon icon-tabler icons-tabler-outline icon-tabler-circle"
			><path stroke="none" d="M0 0h24v24H0z" fill="none" /><path
				d="M12 12m-9 0a9 9 0 1 0 18 0a9 9 0 1 0 -18 0"
			/></svg
		>
	</Button>
	<!-- 	<button 
					on:click={() => {
						dispatch('color', { color: background })
						paletteColor = background
					}}
					>
		<span class="hidden">
			Select the background color to clear the canvas
		</span>
		<svg  xmlns="http://www.w3.org/2000/svg"  width="24"  height="24"  viewBox="0 0 24 24"  fill="none"  stroke="currentColor"  stroke-width="2"  stroke-linecap="round"  stroke-linejoin="round"  class="icon icon-tabler icons-tabler-outline icon-tabler-eraser"><path stroke="none" d="M0 0h24v24H0z" fill="none"/><path d="M19 20h-10.5l-4.21 -4.3a1 1 0 0 1 0 -1.41l10 -10a1 1 0 0 1 1.41 0l5 5a1 1 0 0 1 0 1.41l-9.2 9.3" /><path d="M18 13.3l-6.3 -6.3" /></svg>
	</button> -->

	<Button title="Limpiar dibujo" variant="destructive" size="sm" on:click={clearCanvas}>
		<svg
			xmlns="http://www.w3.org/2000/svg"
			width="24"
			height="24"
			viewBox="0 0 24 24"
			fill="none"
			stroke="currentColor"
			stroke-width="2"
			stroke-linecap="round"
			stroke-linejoin="round"
			class="icon icon-tabler icons-tabler-outline icon-tabler-paint-off"
			><path stroke="none" d="M0 0h24v24H0z" fill="none" /><path
				d="M7 3h10a2 2 0 0 1 2 2v2a2 2 0 0 1 -2 2h-4m-4 0h-2a2 2 0 0 1 -2 -2v-2"
			/><path d="M19 6h1a2 2 0 0 1 2 2a5 5 0 0 1 -5 5m-4 0h-1v2" /><path
				d="M10 15m0 1a1 1 0 0 1 1 -1h2a1 1 0 0 1 1 1v4a1 1 0 0 1 -1 1h-2a1 1 0 0 1 -1 -1z"
			/><path d="M3 3l18 18" /></svg
		>
	</Button>
	<input id="lineWidth" type="range" min="1" max="20" bind:value={lineWidth} class="slider" />
</section>

<style>
	section {
		--size: 1.75rem;
		padding: 0.25rem;
		display: flex;
		align-items: center;
		gap: 0 1rem;
	}

	section > div {
		flex-grow: 1;
	}

	section > svg {
		flex-shrink: 0;
	}

	div {
		display: flex;
		gap: 0 0.5rem;
		align-items: center;
		overflow-x: auto;
	}

	div::-webkit-scrollbar {
		height: 0.25rem;
	}

	div::-webkit-scrollbar-track {
		background: hsl(0, 0%, 100%);
	}

	div::-webkit-scrollbar-thumb {
		background: currentColor;
	}

	button,
	section > svg {
		width: var(--size);
		height: var(--size);
	}

	button {
		cursor: pointer;
		border-radius: 50%;
		margin: 0;
	}

	section > svg {
		display: block;
	}
</style>
