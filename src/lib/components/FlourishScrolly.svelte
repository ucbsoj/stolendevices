<script lang="ts">
  import { browser } from '$app/environment';

  export let src: string = '';
  export let text1: string = '';
  export let text2: string = '';
  export let text3: string = '';
</script>

{#if browser}
  <div class="scrolly">

    <div class="steps">
      {#if text1}
        <div class="step"><p>{text1}</p></div>
      {/if}
      {#if text2}
        <div class="step"><p>{text2}</p></div>
      {/if}
      {#if text3}
        <div class="step"><p>{text3}</p></div>
      {/if}
    </div>

    <div class="sticky-chart">
      <div class="chart-box">
        <iframe
          src={src}
          frameborder="0"
          scrolling="no"
          sandbox="allow-same-origin allow-forms allow-scripts allow-downloads allow-popups allow-popups-to-escape-sandbox allow-top-navigation-by-user-activation"
          title="Flourish chart"
          style="width:100%; height:100%; border:none;"
        ></iframe>
      </div>
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
    padding: 0 2rem 60vh 2rem;
    z-index: 1;
    color: #fff;
  }

  .step {
    min-height: 70vh;
    display: flex;
    align-items: center;
    font-size: 1.1rem;
    line-height: 1.8;
    padding: 2rem 0;
  }

  .step:first-child {
    padding-top: 2rem;
  }

  .sticky-chart {
    position: sticky;
    top: 5vh;
    width: 60%;
    height: 100vh;
    flex-shrink: 0;
    display: flex;
    align-items: center;
    justify-content: center;
    background: #061637;
    padding: 2rem;
    align-self: flex-start;
  }

  .chart-box {
    width: 100%;
    height: 60vh;
    border-radius: 10px;
    overflow: hidden;
  }

  @media (max-width: 768px) {
    .scrolly {
      flex-direction: column;
      width: 100%;
      left: 0;
      transform: none;
    }
    .sticky-chart {
      position: relative;
      width: 100%;
      height: auto;
      top: 0;
      padding: 1rem;
    }
    .chart-box {
      height: 60vw;
      min-height: 280px;
    }
    .steps {
      width: 100%;
      padding: 0 1rem 2rem 1rem;
    }
    .step {
      min-height: auto;
      padding: 1.5rem 0;
    }
    .step:first-child {
      padding-top: 1rem;
    }
  }
</style>