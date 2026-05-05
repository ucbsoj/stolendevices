<script lang="ts">
  import { onMount } from 'svelte';

  export let size: string = 'large';

  let anchor: HTMLElement;
  let expanded = false;
  let playing = false;
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
          playing = false;
        }
      },
      { threshold: 0.5 }
    );
    observer.observe(anchor);
    return () => observer.disconnect();
  });

  function close() {
    expanded = false;
    playing = false;
  }
</script>

<div bind:this={anchor} class="embed-anchor" class:large={size === 'large'}>
  <div class="embed-inline">
    <iframe
      style="position:absolute; top:0; left:0; width:100%; height:100%; border:none; pointer-events:none;"
      src="https://media.journalism.berkeley.edu/video/player?video=20260505_bert_interview/playlist.m3u8"
      frameborder="0"
      scrolling="no"
      title="Video preview"
    ></iframe>
    <div class="inline-overlay">
      <div class="scroll-hint">▶ Scroll to expand</div>
    </div>
  </div>
</div>

{#if expanded}
  <div class="backdrop" on:click={close}></div>
{/if}

<div
  class="embed-box"
  class:expanded
  style="--from-top: {rect.top}px; --from-left: {rect.left}px; --from-w: {rect.width}px; --from-h: {rect.height}px;"
>
  <button class="close-btn" on:click={close}>✕</button>

  {#if !playing}
    <button class="play-overlay" on:click={() => playing = true}>
      <div class="play-circle">▶</div>
      <span class="play-label">Play video</span>
    </button>
  {:else}
    <iframe
      style="width:100%; height:100%; border:none; display:block;"
      src="https://media.journalism.berkeley.edu/video/player?video=20260505_bert_interview/playlist.m3u8&autoplay=1"
      frameborder="0"
      allow="autoplay"
      title="Embedded video"
    ></iframe>
  {/if}
</div>

<style>
  .embed-anchor.large {
    width: 100%;
    max-width: 860px;
    margin: 2rem auto;
  }

  .embed-inline {
    position: relative;
    width: 100%;
    padding-bottom: 56.25%;
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
    width: min(90vw, 960px);
    height: min(56.25vw, 540px);
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

  .play-overlay {
    position: absolute;
    inset: 0;
    width: 100%;
    height: 100%;
    background: #061637;
    border: none;
    cursor: pointer;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: 16px;
    color: white;
  }

  .play-circle {
    width: 72px;
    height: 72px;
    border-radius: 50%;
    background: rgba(255,255,255,0.15);
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 1.8rem;
    transition: background 0.2s, transform 0.2s;
  }

  .play-overlay:hover .play-circle {
    background: rgba(255,255,255,0.3);
    transform: scale(1.1);
  }

  .play-label {
    font-size: 0.95rem;
    opacity: 0.75;
    letter-spacing: 0.07em;
  }
</style>