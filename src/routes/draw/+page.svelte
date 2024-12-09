<script lang="ts">
	import ImageArea from '$lib/components/ImageArea.svelte';
	import { Button, buttonVariants } from '$lib/components/ui/button';
	import * as Dialog from '$lib/components/ui/dialog';
	import { onMount } from 'svelte';
	import Palette from '$lib/components/Palette.svelte';

	let status: 'Base' | 'Uploading' | 'Input' | 'Loaded' = 'Base';
	let statusPreview: 'Base' | 'Uploading' | 'Input' | 'Loaded' = 'Base';

	let canvas: HTMLCanvasElement;
	let ctx: CanvasRenderingContext2D;
	let imageInput: HTMLInputElement;
	let selectedImage: HTMLImageElement;
	let editorContainer: HTMLDivElement;
	let isDrawing: boolean;
	let originalImg: CanvasImageSource;
	let shapeMode: '' | 'rect' | 'circle' = '';
	let start: { x: number; y: number };
	let t: any, l: any;
	let isShiftPressed = false;

	const MAX_CANVAS_WIDTH = 800;
	const MAX_CANVAS_HEIGHT = 600;
	let lineWidth = 5;

	window.addEventListener('keydown', (e) => {
		if (e.key === 'Shift') isShiftPressed = true;
	});
	window.addEventListener('keyup', (e) => {
		if (e.key === 'Shift') isShiftPressed = false;
	});

	$: if (ctx) {
		ctx.strokeStyle = paletteColor;
		ctx.lineWidth = lineWidth;
	}

	onMount(() => {
		ctx = canvas.getContext('2d')!;

		handleSize();
	});

	const handleImageUpload = (e: Event) => {
		status = 'Uploading';
		const file = (e.target as HTMLInputElement).files?.[0];
		if (file) {
			status = 'Loaded';
			const reader = new FileReader();
			reader.onload = (e) => {
				selectedImage = new Image();
				selectedImage.onload = () => {
					let width = selectedImage.width;
					let height = selectedImage.height;
					if (width > MAX_CANVAS_WIDTH || height > MAX_CANVAS_HEIGHT) {
						const scale = Math.min(MAX_CANVAS_WIDTH / width, MAX_CANVAS_HEIGHT / height);
						width *= scale;
						height *= scale;
					}
					canvas.width = width;
					canvas.height = height;
					editorContainer.style.width = `${width}px`;
					editorContainer.style.height = `${height}px`;
					drawImage();
				};
				selectedImage.src = e.target?.result as string;
			};
			reader.readAsDataURL(file);
		}
	};

	function drawImage() {
		ctx = canvas.getContext('2d')!;
		if (!ctx || !selectedImage) return;
		ctx.clearRect(0, 0, canvas.width, canvas.height);
		originalImg = selectedImage;
		ctx.drawImage(selectedImage, 0, 0, canvas.width, canvas.height);
	}

	const handleStart = ({ offsetX: x, offsetY: y }: { offsetX: number; offsetY: number }) => {
		if (paletteColor === background) {
			ctx.globalCompositeOperation = 'destination-out';
		} else {
			ctx.globalCompositeOperation = 'source-over';
		}
		isDrawing = true;
		start = { x, y };

		canvas.style.cursor = shapeMode ? 'crosshair' : 'default';
		if (shapeMode === 'rect' || shapeMode === 'circle') {
			ctx.beginPath();
		}
	};

	const handleEnd = () => {
		isDrawing = false;
	};

	const handleMove = ({ offsetX: x1, offsetY: y1 }: { offsetX: any; offsetY: any }) => {
		if (!isDrawing) return;
		const { x, y } = start;

		if (shapeMode === 'rect') {
			clearCanvas();
			/* ctx.strokeStyle = paletteColor; */
			const width = x1 - x;
			const height = isShiftPressed ? width : y1 - y;
			ctx.strokeRect(x, y, width, height);
			return;
		}

		if (shapeMode === 'circle') {
			clearCanvas();
			const radiusX = Math.abs(x1 - x) / 2;
			const radiusY = isShiftPressed ? radiusX : Math.abs(y1 - y) / 2; // Círculo si Shift está presionado
			const centerX = x + radiusX;
			const centerY = y + radiusY;
			ctx.beginPath();
			ctx.ellipse(centerX, centerY, radiusX, radiusY, 0, 0, 2 * Math.PI);
			ctx.stroke();
			return;
		}
		ctx.beginPath();
		ctx.moveTo(x, y);
		ctx.lineTo(x1, y1);
		ctx.closePath();
		ctx.stroke();

		start = { x: x1, y: y1 };
	};

	function getImageWithDraw(): string {
		return canvas.toDataURL('image/png');
	}

	function clearCanvas() {
		ctx.clearRect(0, 0, canvas.width, canvas.height);
		if (originalImg) ctx.drawImage(originalImg, 0, 0, canvas.width, canvas.height);
	}

	let imageEdit = '';

	const handleSave = () => {
		const image = getImageWithDraw();
		statusPreview = 'Loaded';
		imageEdit = image;
		const link = document.createElement('a');
		link.download = 'image.png';
		link.href = imageEdit;
		link.click();
		ctx.clearRect(0, 0, canvas.width, canvas.height);
		ctx.fillStyle = '#ffffff';
	};

	const handleSize = () => {
		const { top, left } = canvas.getBoundingClientRect();
		t = top;
		l = left;
	};

	$: {
		if (canvas) {
			canvas.style.cursor = shapeMode ? 'crosshair' : 'default';
		}
	}

	const background = '#fff';
	let paletteColor = '#d7c44c';
</script>

<main class="flex flex-col items-center justify-center gap-6">
	<div class="flex flex-col items-center justify-center gap-4">
		{#if statusPreview === 'Loaded'}
			<img src={imageEdit} alt="Preview" class="h-full w-full" />
			<Button variant="outline" on:click={() => (statusPreview = 'Base')}>Reset</Button>
		{/if}
		<Dialog.Root>
			<Dialog.Trigger
				class={buttonVariants({ variant: 'default' })}
				on:click={() => (status = 'Input')}>Editar Imagen con trazos</Dialog.Trigger
			>
			<Dialog.Content class="!w-fit !max-w-[900px]">
				<Dialog.Header>
					<Dialog.Title>Editor de Imagen</Dialog.Title>
				</Dialog.Header>
				<div class="flex flex-col gap-4">
					<ImageArea src="..." {status}>
						{#if status === 'Loaded'}
							<div
								class="relative overflow-hidden border border-slate-50"
								bind:this={editorContainer}
							>
								<canvas
									class="block max-w-full"
									bind:this={canvas}
									on:mousedown={handleStart}
									on:touchstart={(e) => {
										const { clientX, clientY } = e.touches[0];
										handleStart({
											offsetX: clientX - l,
											offsetY: clientY - t
										});
									}}
									on:mouseup={handleEnd}
									on:touchend={handleEnd}
									on:mouseleave={handleEnd}
									on:mousemove={handleMove}
									on:touchmove={(e) => {
										const { clientX, clientY } = e.touches[0];
										handleMove({
											offsetX: clientX - l,
											offsetY: clientY - t
										});
									}}
								></canvas>
							</div>
						{:else if status === 'Input'}
							<label for="file" class="mt-2 cursor-pointer text-sm text-gray-500"
								>Sube tu imagen aquí</label
							>
							<input
								id="file"
								name="file"
								type="file"
								accept="image/*"
								class="hidden"
								bind:this={imageInput}
								on:change={handleImageUpload}
							/>
						{/if}
					</ImageArea>
					<div class="flex w-full items-center gap-2 pt-4">
						<Palette
							{paletteColor}
							{background}
							{clearCanvas}
							bind:shapeMode
							bind:lineWidth
							on:color={({ detail }) => {
								paletteColor = detail.color;
							}}
						/>
					</div>
					<Dialog.Close class="flex justify-end">
						<Button variant="default" on:click={handleSave}>Guardar</Button>
					</Dialog.Close>
				</div>
			</Dialog.Content>
		</Dialog.Root>
	</div>
</main>
