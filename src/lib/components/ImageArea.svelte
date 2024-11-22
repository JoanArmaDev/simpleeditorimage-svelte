<script lang="ts">
	import { AspectRatio } from '$lib/components/ui/aspect-ratio';

	export let status: 'Base' | 'Uploading' | 'Input' | 'Loaded' = 'Base';
	export let src: string;
</script>

<div class="w-full rounded-lg border border-dashed border-gray-300 p-4">
	<div class="flex flex-col items-center justify-center">
		{#if status === 'Loaded'}
			<slot>
				<div>
					<AspectRatio ratio={16 / 9} class="bg-muted">
						<img {src} alt="..." class="rounded-md object-cover" />
					</AspectRatio>
				</div>
			</slot>
		{:else}
			<div class="flex flex-col items-center justify-center">
				{#if status === 'Base'}
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
						class="icon icon-tabler icons-tabler-outline icon-tabler-photo"
						><path stroke="none" d="M0 0h24v24H0z" fill="none" /><path d="M15 8h.01" /><path
							d="M3 6a3 3 0 0 1 3 -3h12a3 3 0 0 1 3 3v12a3 3 0 0 1 -3 3h-12a3 3 0 0 1 -3 -3v-12z"
						/><path d="M3 16l5 -5c.928 -.893 2.072 -.893 3 0l5 5" /><path
							d="M14 14l1 -1c.928 -.893 2.072 -.893 3 0l3 3"
						/></svg
					>
				{:else if status === 'Uploading'}
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
						class="icon icon-tabler icons-tabler-outline icon-tabler-loader-quarter animate-spin"
						><path stroke="none" d="M0 0h24v24H0z" fill="none" /><path d="M12 6l0 -3" /><path
							d="M6 12l-3 0"
						/><path d="M7.75 7.75l-2.15 -2.15" /></svg
					>
				{:else}
					<svg
						xmlns="http://www.w3.org/2000/svg"
						fill="none"
						viewBox="0 0 24 24"
						stroke-width="1.5"
						stroke="currentColor"
						class="h-12 w-12"
					>
						<path stroke-linecap="round" stroke-linejoin="round" d="M12 6v6m0 0v6m0-6h6m-6 0H6" />
					</svg>
				{/if}
			</div>
			{#if status === 'Base'}
				<p class="mt-2 text-sm text-gray-500">Tu imagen se vera aquí</p>
			{:else if status === 'Uploading'}
				<span>Cargando</span>
			{:else}
				<slot></slot>
			{/if}
		{/if}
	</div>
</div>
