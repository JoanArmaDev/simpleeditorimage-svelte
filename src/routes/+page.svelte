<script lang="ts">
	import DraggableText from '$lib/components/DraggableText.svelte';
	import ImageArea from '$lib/components/ImageArea.svelte';
	import { Button, buttonVariants } from '$lib/components/ui/button';
	import * as Dialog from '$lib/components/ui/dialog';
	import { Input } from '$lib/components/ui/input';
	import { onMount } from 'svelte';
	import type { DragEventData, DragOptions } from '@neodrag/svelte';

	let status: 'Base' | 'Uploading' | 'Input' | 'Loaded' = 'Base';
  let statusPreview: 'Base' | 'Uploading' | 'Input' | 'Loaded' = 'Base';

	let canvas: HTMLCanvasElement;
	let ctx: CanvasRenderingContext2D;
	let imageInput: HTMLInputElement;
	let selectedImage: HTMLImageElement;
	let editorContainer: HTMLDivElement;

	let text = 'Tu texto aquí';
	let fontSize = 32;
	let textColor = '#ffffff';
	let textX = 50;
	let textY = 50;
	let options: DragOptions = {
		axis: 'both',
		defaultPosition: { x: textX, y: textY }
	};

	const MAX_CANVAS_WIDTH = 800;
	const MAX_CANVAS_HEIGHT = 600;

	onMount(() => {
		ctx = canvas.getContext('2d')!;
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
		ctx.drawImage(selectedImage, 0, 0, canvas.width, canvas.height);
	}

	function handlePositionChange(newX: number, newY: number) {
		textX = newX;
		textY = newY;
	}

	function getImageWithText(): string {
		if (!ctx || !selectedImage) return '';
		drawImage();
		ctx.font = `${fontSize}px Arial`;
		ctx.fillStyle = textColor;
		ctx.fillText(text, textX, textY + fontSize);
		return canvas.toDataURL('image/png');
	}
	let imageEdit = '';

	const handleSave = () => {
		const image = getImageWithText();
		statusPreview = 'Loaded';
		imageEdit = image;
		const link = document.createElement('a');
		link.download = 'image.png';
		link.href = imageEdit;
		link.click();
		ctx.clearRect(0, 0, canvas.width, canvas.height);
		ctx.fillStyle = '#ffffff';
	};
</script>

<main class="flex flex-col items-center justify-center gap-6">
<Button variant="outline" disabled>
	Próximamente
</Button>
</main>
