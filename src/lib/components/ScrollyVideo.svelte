<script lang="ts">
  import { onMount } from 'svelte';
  import { browser } from '$app/environment';
  import { asset } from '$app/paths';

  export let video1: string = '';
  export let imgSrc: string = '';
  export let text1: string = '';
  export let text2: string = '';

  let step = 0;
  let step1el: HTMLElement;
  let step2el: HTMLElement;

  onMount(() => {
    const io = new IntersectionObserver(
      (entries) => {
        entries.forEach(entry => {
          if (entry.target === step1el && entry.isIntersecting) step = 0;
          if (entry.target === step2el && entry.isIntersecting) step = 1;
        });
      },
      { threshold: 0.5 }
    );
    io.observe(step1el);
    io.observe(step2el);
    return () => io.disconnect();
  });
</script>

{#if browser}
  <div class="scrolly">

    <div class="steps">
      <div class="step" bind:this={step1el}>
        <p>{text1}</p>
      </div>
      <div class="step" bind:this={step2el}>
        <p>{text2}</p>
      </div>
    </div>

    <div class="sticky-media">
      {#if step === 0}
        <video
          src={asset('/' + video1)}
          autoplay
          muted
          loop
          playsinline
          class="media-fit"
        />
      {:else}
        <img src={imgSrc} alt="Step 2" class="media-fit" />
      {/if}
    </div>

  </div>
{/if}

<style>
  .scrolly {
    display: flex;
    flex-direction: row;
    align-items: flex-start;
    width: 100vw;
    position: relative;
    left: 50%;
    transform: translateX(-50%);
    background: #061637;
  }

  .steps {
    width: 40%;
    flex-shrink: 0;
    display: flex;
    flex-direction: column;
    padding: 0 2rem;
    z-index: 1;
    background: #061637;
    color: #fff;
  }

  .step {
    min-height: 100vh;
    display: flex;
    align-items: center;
    font-size: 1.1rem;
    line-height: 1.8;
  }

  .step:first-child {
    padding-top: 2rem;
    min-height: 60vh;
  }

  .sticky-media {
    position: sticky;
    top: 0;
    width: 60%;
    height: 100vh;
    flex-shrink: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    overflow: hidden;
    background: #061637;
    padding: 2rem;
  }

  .media-fit {
    width: 100%;
    height: auto;
    max-height: 70vh;
    max-width: 90%;
    object-fit: contain;
    display: block;
    border-radius: 8px;
  }

  @media (max-width: 768px) {
    .scrolly {
      flex-direction: column;
      width: 100%;
      left: 0;
      transform: none;
    }
    .sticky-media {
      position: relative;
      width: 100%;
      height: auto;
      top: 0;
      padding: 1rem;
    }
    .steps {
      width: 100%;
      padding: 0 1rem;
    }
  }
</style>