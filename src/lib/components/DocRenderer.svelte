<script lang="ts" context="module">
	export type Block =
		| { type: 'html'; html: string }
		| {
				type: 'shortcode';
				name: string;
				attrs: Record<string, any>;
		  };
</script>

<script lang="ts">
	import { browser } from '$app/environment';

	export let blocks: Block[] = [];

	const modules = import.meta.glob('$lib/components/**/*.svelte', { eager: true });
	const registry: Record<string, any> = {};

	for (const [path, mod] of Object.entries(modules)) {
		const file = path.split('/').pop() ?? '';
		if (file === 'DocRenderer.svelte') continue;
		const base = file.replace(/\.svelte$/, '');
		// @ts-expect-error — Svelte component default export
		registry[base] = mod.default;
	}

	// TEMPORARY DEBUG — paste console output here
	console.log('registry keys:', Object.keys(registry));
	console.log('looking for VideoPop:', registry['VideoPop']);

	function getComponent(name: string) {
		console.log('looking up:', name, '→', registry[name]);
		return registry[name];
	}

	function toCamel(s: string) {
		return s.replace(/-([a-z])/g, (_, c) => c.toUpperCase());
	}

	function normalizeAttrs(attrs: Record<string, any>) {
		const out: Record<string, any> = {};
		for (const [k, v] of Object.entries(attrs ?? {})) {
			const key = toCamel(k);
			if (v !== null && typeof v === 'object') { out[key] = v; continue; }
			if (v === 'true') { out[key] = true; continue; }
			if (v === 'false') { out[key] = false; continue; }
			if (typeof v === 'string' && v.trim() !== '' && !isNaN(Number(v))) { out[key] = Number(v); continue; }
			out[key] = v;
		}
		return out;
	}
</script>

{#each blocks as block, i (i)}
	{#if block.type === 'html'}
		{@html block.html}
	{:else if block.type === 'shortcode'}
		{#if getComponent(block.name)}
			<svelte:component
				this={getComponent(block.name)}
				{...normalizeAttrs(block.attrs)}
			/>
		{:else}
			<!-- DEBUG: show unmatched shortcodes -->
			<p style="color:red; border:1px solid red; padding:8px;">
				⚠️ No component found for: <strong>{block.name}</strong>
			</p>
		{/if}
	{/if}
{/each}