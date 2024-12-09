<script lang="ts">
	import '../app.css';
	import { Toaster } from '$lib/components/ui/sonner';
	import { page } from '$app/stores';
	import Button from '$lib/components/ui/button/button.svelte';
	let { children } = $props();
</script>

<Toaster />
<div class="flex flex-col items-center gap-6">
	<h1 class="pt-10 text-center text-4xl font-bold">Editor Simple de Imágenes</h1>
	<nav class="m-auto flex max-w-80 justify-center gap-5 rounded-lg border border-gray-500 p-1">
		<ul class="flex gap-4 text-center">
			<li>
				<Button class="{$page.url.pathname === '/' ? 'bg-accent' : ''} ease-in transition-all" variant="ghost" href="/" aria-current={$page.url.pathname === '/'}
					>Inicio</Button
				>
			</li>
			<li>
				<Button class="{$page.url.pathname === '/text' ? 'bg-accent' : ''} ease-in transition-all" variant="ghost" href="/text" aria-current={$page.url.pathname === '/text'}
					>Solo Texto</Button
				>
			</li>
			<li>
				<Button class="{$page.url.pathname === '/draw' ? 'bg-accent' : ''} ease-in transition-all" variant="ghost" href="/draw" aria-current={$page.url.pathname === '/draw'}
					>Solo Dibujo</Button
				>
			</li>
		</ul>
	</nav>
	{@render children()}
</div>
