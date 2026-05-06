<script lang="ts">
  import { onMount } from 'svelte';
  import { asset } from '$app/paths';
  export let src: string = '';
  export let size: string = 'fit';
  export let controls: boolean = true;
  export let autoplay: boolean = false;
  export let playsinline: boolean = true;
  export let loop: boolean = false;

  let anchor: HTMLElement;
  let expanded = false;
  let rect = { top: 0, left: 0, width: 0, height: 0 };

  onMount(() => {
    if (!anchor) return;
    const observer = new IntersectionObserver(
      ([entry]) => {
        if (entry.isIntersecting) {
          const r = anchor.getBoundingClientRect();
          rect = { top: r.top, left: r.left, width: r.width, height: r.height };
          expanded = true;
        } else {
          expanded = false;
        }
      },
      { threshold: 0.5 }
    );
    observer.observe(anchor);
    return () => observer.disconnect();
  });

  function close() {
    expanded = false;
  }

  $: videoSrc = src.startsWith('http') ? src : `/${src}`;
</script>

<!-- Inline preview stays in doc flow -->
<div bind:this={anchor} class="embed-anchor" class:fit={size === 'fit'} class:large={size === 'large'}>
  <div class="embed-inline">
    <video
      src={asset(videoSrc)}
      muted
      autoplay
      playsinline
      loop
      style="position:absolute; top:0; left:0; width:100%; height:100%; object-fit:cover; pointer-events:none;"
    ></video>
    <div class="inline-overlay">
      <div class="scroll-hint">▶ Scroll to expand</div>
    </div>
  </div>
</div>

<!-- Blurred backdrop -->
{#if expanded}
  <button type="button" aria-label="Close" class="backdrop" on:click={close}></button> 
{/if}

<!-- Expanding video box -->
<div
  class="embed-box"
  class:expanded
  style="
    --from-top: {rect.top}px;
    --from-left: {rect.left}px;
    --from-w: {rect.width}px;
    --from-h: {rect.height}px;
  "
>
  <button class="close-btn" on:click={close}>✕</button>
  <!-- svelte-ignore a11y_media_has_caption -->
  <video
    src={asset(videoSrc)}
    controls={controls}
    autoplay={autoplay}
    playsinline={playsinline}
    loop={loop}
    style="width:100%; height:100%; object-fit:contain; display:block; background:#000;"
 ></video>
</div>

<style>
  /* Vertical 9:16 aspect ratio for both inline and expanded */
  .embed-anchor.fit {
    width: 100%;
    max-width: 340px;
    margin: 2rem auto;
  }

  .embed-anchor.large {
    width: 100%;
    max-width: 480px;
    margin: 2rem auto;
  }

  .embed-inline {
    position: relative;
    width: 100%;
    padding-bottom: 177.78%; /* 16:9 flipped = 9:16 */
    height: 0;
    border-radius: 10px;
    overflow: hidden;
    border: 2px solid rgba(255,255,255,0.12);
  }

  .inline-overlay {
    position: absolute;
    inset: 0;
    background: rgba(0,0,0,0.45);
    display: flex;
    align-items: center;
    justify-content: center;
    pointer-events: none;
  }

  .scroll-hint {
    color: white;
    font-size: 0.9rem;
    letter-spacing: 0.08em;
    opacity: 0.8;
    background: rgba(0,0,0,0.4);
    padding: 8px 16px;
    border-radius: 20px;
    backdrop-filter: blur(4px);
  }

  .backdrop {
    position: fixed;
    inset: 0;
    background: rgba(0,0,0,0.55);
    backdrop-filter: blur(6px);
    -webkit-backdrop-filter: blur(6px);
    z-index: 9998;
    animation: fadeIn 0.35s ease;
  }

  @keyframes fadeIn {
    from { opacity: 0; }
    to { opacity: 1; }
  }

  .embed-box {
    position: fixed;
    top: var(--from-top);
    left: var(--from-left);
    width: var(--from-w);
    height: var(--from-h);
    background: #000;
    border-radius: 10px;
    overflow: hidden;
    z-index: 9999;
    transition:
      top 0.45s cubic-bezier(0.4, 0, 0.2, 1),
      left 0.45s cubic-bezier(0.4, 0, 0.2, 1),
      width 0.45s cubic-bezier(0.4, 0, 0.2, 1),
      height 0.45s cubic-bezier(0.4, 0, 0.2, 1),
      border-radius 0.45s ease,
      box-shadow 0.45s ease,
      opacity 0.2s ease;
    pointer-events: none;
    opacity: 0;
  }

  .embed-box.expanded {
    top: 50%;
    left: 50%;
    /* Vertical expanded size — tall not wide */
    width: min(50vh, 400px);
    height: min(88vh, 720px);
    transform: translate(-50%, -50%);
    box-shadow: 0 32px 80px rgba(0,0,0,0.7);
    border-radius: 14px;
    pointer-events: all;
    opacity: 1;
  }

  .close-btn {
    position: absolute;
    top: 10px;
    right: 14px;
    background: rgba(255,255,255,0.15);
    color: white;
    border: none;
    border-radius: 50%;
    width: 36px;
    height: 36px;
    font-size: 0.95rem;
    cursor: pointer;
    z-index: 10;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: background 0.2s;
  }

  .close-btn:hover {
    background: rgba(255,255,255,0.3);
  }
</style>