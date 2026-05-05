<script lang="ts">
  import { onMount } from 'svelte';

  let canvas: HTMLCanvasElement;
  let stage: HTMLDivElement;
  let stolenCount = 0;

  const TOTAL = 1400000;

  onMount(() => {
    const ctx = canvas.getContext('2d')!;
    let W: number, H: number;
    let phones: any[] = [];
    let landed: any[] = [];
    let stolen = 0;
    let lastTime = 0;
    let animId: number;

    function resize() {
      W = stage.clientWidth;
      H = stage.clientHeight;
      const dpr = window.devicePixelRatio || 1;
      canvas.width = W * dpr;
      canvas.height = H * dpr;
      canvas.style.width = W + 'px';
      canvas.style.height = H + 'px';
      ctx.scale(dpr, dpr);
    }

    function spawnPhone() {
      phones.push({
        x: 30 + Math.random() * (W - 60),
        y: -10,
        vy: 1.2 + Math.random() * 1.8,
        targetY: H * (0.55 + Math.random() * 0.35),
        size: 9 + Math.random() * 7,
        alpha: 0.7 + Math.random() * 0.3,
      });
    }

    function drawPhone(x: number, y: number, size: number, alpha: number) {
      ctx.save();
      ctx.globalAlpha = alpha;
      const w = size * 0.6, h = size, r = size * 0.15;
      ctx.strokeStyle = '#fff';
      ctx.lineWidth = 1;
      ctx.beginPath();
      ctx.roundRect(x - w / 2, y - h / 2, w, h, r);
      ctx.stroke();
      ctx.fillStyle = 'rgba(255,255,255,0.08)';
      ctx.fill();
      ctx.beginPath();
      ctx.arc(x, y + h / 2 - size * 0.12, size * 0.08, 0, Math.PI * 2);
      ctx.fillStyle = 'rgba(255,255,255,0.5)';
      ctx.fill();
      ctx.restore();
    }

    function drawPile() {
      const show = Math.min(landed.length, 1200);
      for (let i = 0; i < show; i++) {
        const l = landed[i];
        ctx.save();
        ctx.globalAlpha = 0.12;
        ctx.strokeStyle = '#fff';
        ctx.lineWidth = 0.5;
        const w = l.size * 0.6, h = l.size;
        ctx.beginPath();
        ctx.roundRect(l.x - w / 2, l.y - h / 2, w, h, 2);
        ctx.stroke();
        ctx.restore();
      }
    }

    function loop(ts: number) {
      if (!lastTime) lastTime = ts;
      const dt = Math.min(ts - lastTime, 60);
      lastTime = ts;

      const toSpawn = Math.round(dt * 0.15);
      for (let i = 0; i < toSpawn && stolen < TOTAL; i++) {
        spawnPhone();
        stolen++;
      }

      ctx.clearRect(0, 0, W, H);
      ctx.fillStyle = '#061637';
      ctx.fillRect(0, 0, W, H);

      drawPile();

      for (let i = phones.length - 1; i >= 0; i--) {
        const p = phones[i];
        p.y += p.vy * (dt / 16);
        if (p.y >= p.targetY) {
          landed.push({ x: p.x, y: p.targetY, size: p.size });
          if (landed.length > 2000) landed.splice(0, 200);
          phones.splice(i, 1);
        } else {
          drawPhone(p.x, p.y, p.size, p.alpha);
        }
      }

      stolenCount = Math.min(stolen, TOTAL);
      animId = requestAnimationFrame(loop);
    }

    resize();
    window.addEventListener('resize', resize);
    animId = requestAnimationFrame(loop);

    return () => {
      cancelAnimationFrame(animId);
      window.removeEventListener('resize', resize);
    };
  });
</script>

<div class="root">

  <div class="stage" bind:this={stage}>
    <canvas bind:this={canvas}></canvas>
    <div class="overlay">
      <div>
        <div class="big">{stolenCount.toLocaleString()}</div>
        <div class="sub">phones stolen in the U.S. in 2023</div>
        <div class="src">
          Source: <a href="https://www.crisis24.com/articles/increasing-rates-of-phone-thefts-worldwide-pose-significant-data-security-risks">Federal Communications Commission</a>
        </div>
      </div>
      <div class="rate-box">
        <div class="rate">1 stolen every 22 seconds</div>
        <div class="rate">~160 per hour</div>
        <div class="rate">~3.8M per day</div>
      </div>
    </div>
  </div>

  <div class="stats">
    <div class="stat">
      <div class="val">$69B+</div>
      <div class="lbl">Asia Pacific used & refurbished phone market, 2026 — the fastest-growing region globally</div>
      <div class="src">Source: <a href="https://www.mordorintelligence.com/industry-reports/used-and-refurbished-smartphone-market">Mordor Intelligence</a></div>
    </div>
    <div class="stat">
      <div class="val">4M+</div>
      <div class="lbl">prepaid "sold-not-active" devices trafficked annually in the U.S.</div>
      <div class="src">Source: <a href="https://www.trustonic.com/opinion/how-big-of-a-problem-is-mobile-supply-chain-device-theft/">GSMA via Trustonic</a></div>
    </div>
    <div class="stat">
      <div class="val">$900M+</div>
      <div class="lbl">annual losses to mobile operators from device trafficking</div>
      <div class="src">Source: <a href="https://www.trustonic.com/opinion/how-big-of-a-problem-is-mobile-supply-chain-device-theft/">GSMA via Trustonic</a></div>
    </div>
  </div>

  <div class="bar-wrap">
    <div class="bar-header">
      <span>Stolen phones vs trafficked devices</span>
      <span class="bar-src">GSMA via Trustonic</span>
    </div>
    <div class="bar-row">
      <span class="bar-lbl">1.4M stolen</span>
      <div class="bar-track"><div class="bar-fill" style="width:35%"></div></div>
    </div>
    <div class="bar-row">
      <span class="bar-lbl">4M+ trafficked</span>
      <div class="bar-track"><div class="bar-fill muted" style="width:100%"></div></div>
    </div>
  </div>

</div>

<style>
  @font-face {
    font-family: 'BC Barrel';
    src: url('/fonts/bc-barell-font-family/BCBarellTEST-Regular.otf') format('opentype');
    font-weight: normal;
    font-style: normal;
  }

  @font-face {
    font-family: 'BC Barrel';
    src: url('/fonts/bc-barell-font-family/BCBarellTEST-ExtendedBlack.otf') format('opentype');
    font-weight: 900;
    font-style: normal;
  }

  .root {
    background: #061637;
    border-radius: 0 0 24px 24px;
    overflow: hidden;
    font-family: 'BC Barrel', sans-serif;
    color: #fff;
    width: 100%;
  }

  .stage {
    position: relative;
    width: 100%;
    height: 560px;
  }

  canvas {
    display: block;
    width: 100%;
    height: 100%;
  }

  .overlay {
    position: absolute;
    inset: 0;
    display: flex;
    justify-content: space-between;
    align-items: flex-start;
    padding: 36px 40px;
    pointer-events: none;
  }

  .big {
    font-size: clamp(48px, 7vw, 88px);
    font-weight: 500;
    line-height: 1;
    letter-spacing: -1px;
  }

  .sub {
    font-size: clamp(16px, 1.8vw, 22px);
    color: rgba(255,255,255,0.5);
    margin-top: 8px;
    max-width: 320px;
    line-height: 1.4;
  }

  .src {
    font-size: 13px;
    color: rgba(255,255,255,0.3);
    margin-top: 5px;
  }

  .src a {
    color: rgba(255,255,255,0.4);
    text-decoration: none;
    pointer-events: all;
  }

  .src a:hover {
    text-decoration: underline;
  }

  .rate-box {
    text-align: right;
  }

  .rate {
    font-size: clamp(15px, 1.6vw, 20px);
    color: rgba(255,255,255,0.55);
    margin-bottom: 6px;
  }

  .stats {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 1px;
    background: rgba(255,255,255,0.08);
    border-top: 1px solid rgba(255,255,255,0.08);
  }

  .stat {
    background: #061637;
    padding: 28px 32px;
  }

  .val {
    font-size: clamp(28px, 3.5vw, 42px);
    font-weight: 500;
  }

  .lbl {
    font-size: clamp(14px, 1.4vw, 18px);
    color: rgba(255,255,255,0.45);
    margin-top: 8px;
    line-height: 1.5;
  }

  .stat .src {
    margin-top: 10px;
    font-size: 13px;
  }

  .bar-wrap {
    padding: 28px 32px 32px;
    border-top: 1px solid rgba(255,255,255,0.08);
  }

  .bar-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    font-size: 16px;
    color: rgba(255,255,255,0.45);
    margin-bottom: 16px;
  }

  .bar-src {
    font-size: 13px;
    color: rgba(255,255,255,0.25);
  }

  .bar-row {
    display: flex;
    align-items: center;
    gap: 16px;
    margin-bottom: 12px;
  }

  .bar-lbl {
    font-size: 15px;
    color: rgba(255,255,255,0.4);
    width: 140px;
    flex-shrink: 0;
  }

  .bar-track {
    flex: 1;
    height: 7px;
    border-radius: 3px;
    background: rgba(255,255,255,0.1);
    overflow: hidden;
  }

  .bar-fill {
    height: 100%;
    border-radius: 3px;
    background: #fff;
  }

  .bar-fill.muted {
    background: rgba(255,255,255,0.35);
  }

  @media (max-width: 600px) {
    .stage { height: 380px; }
    .overlay { padding: 16px 20px; }
    .stats { grid-template-columns: 1fr; }
    .stat { padding: 20px 24px; }
    .bar-wrap { padding: 20px 24px 24px; }
    .bar-lbl { width: 110px; }
  }
</style>