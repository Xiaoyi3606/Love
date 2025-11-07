<!DOCTYPE html>
<html lang="zh-CN">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>邓悠然，我喜欢你</title>
  <style>
    :root {
      --bg: #0b0f1a;
      --accent: #ff6bb3;
      --text: #ffffff;
      --soft: #ffd1e6;
    }
    * { box-sizing: border-box; }
    html, body {
      height: 100%;
      margin: 0;
      background: radial-gradient(1200px circle at 70% 20%, #12172a 0%, #0b0f1a 40%, #060812 100%);
      color: var(--text);
      font-family: "Helvetica Neue", "PingFang SC", "Microsoft YaHei", system-ui, -apple-system, Segoe UI, Roboto, Arial, sans-serif;
      overflow: hidden;
    }
    /* 背景烟花画布 */
    canvas#fireworks {
      position: fixed;
      inset: 0;
      width: 100vw;
      height: 100vh;
      display: block;
      z-index: 0;
      background: transparent;
    }
    /* 中央告白卡片 */
    .center {
      position: relative;
      z-index: 2;
      min-height: 100%;
      display: grid;
      place-items: center;
      padding: 24px;
      text-align: center;
    }
    .card {
      width: min(720px, 92vw);
      backdrop-filter: blur(10px) saturate(120%);
      background: rgba(255, 255, 255, 0.06);
      border: 1px solid rgba(255, 255, 255, 0.18);
      border-radius: 24px;
      padding: 32px 28px;
      box-shadow: 0 40px 120px rgba(255, 105, 180, 0.18), 0 12px 24px rgba(0,0,0,0.4);
      animation: floatUp 1.8s ease both;
    }
    @keyframes floatUp {
      from { transform: translateY(18px); opacity: 0; }
      to   { transform: translateY(0);   opacity: 1; }
    }
    .heart {
      width: 120px; height: 120px; margin: 0 auto 12px;
      position: relative;
      filter: drop-shadow(0 6px 18px rgba(255, 105, 180, 0.4));
    }
    .heart::before, .heart::after {
      content: "";
      position: absolute;
      width: 60px; height: 96px;
      background: linear-gradient(180deg, #ff7ab5 0%, #ff3e9e 50%, #ff0066 100%);
      border-radius: 60px 60px 0 0;
      transform: rotate(-45deg);
      transform-origin: 0 100%;
      left: 30px; top: 12px;
      animation: pulse 2s ease-in-out infinite;
    }
    .heart::after {
      transform: rotate(45deg);
      transform-origin: 100% 100%;
      left: auto; right: 30px;
    }
    @keyframes pulse {
      0%, 100% { filter: brightness(1); }
      50% { filter: brightness(1.25); }
    }

    h1 {
      margin: 10px 0 6px;
      font-size: clamp(26px, 4.4vw, 42px);
      letter-spacing: 1px;
    }
    h2 {
      margin: 4px 0 18px;
      font-weight: 500;
      font-size: clamp(18px, 2.8vw, 22px);
      color: var(--soft);
    }
    .message {
      font-size: clamp(16px, 2.6vw, 20px);
      line-height: 1.8;
      color: #e9efff;
      margin: 10px 0 18px;
    }
    .signature {
      margin-top: 10px;
      font-size: 16px;
      color: #bcd0ff;
    }
    .btns {
      display: flex;
      gap: 12px;
      justify-content: center;
      flex-wrap: wrap;
      margin-top: 16px;
    }
    .btn {
      border: none;
      outline: none;
      padding: 10px 18px;
      border-radius: 999px;
      font-weight: 600;
      letter-spacing: 0.3px;
      cursor: pointer;
      transition: transform .15s ease, box-shadow .2s ease, background .3s ease;
    }
    .btn-primary {
      background: linear-gradient(90deg, #ff7ab5, #ff3e9e);
      color: white;
      box-shadow: 0 10px 24px rgba(255, 105, 180, .35);
    }
    .btn-primary:hover { transform: translateY(-2px); }
    .btn-ghost {
      background: rgba(255,255,255,.12);
      color: #ffe8f2;
      border: 1px solid rgba(255,255,255,.22);
      backdrop-filter: blur(6px);
    }
    .note {
      margin-top: 14px;
      font-size: 13px;
      opacity: .8;
      color: #c7d6ff;
    }
    footer {
      position: fixed;
      bottom: 10px; left: 0; right: 0;
      text-align: center;
      font-size: 12px;
      color: #89a4ff;
      z-index: 1;
      opacity: .8;
    }
    a.inline {
      color: var(--soft);
      text-decoration: underline;
      text-underline-offset: 3px;
    }
    /* 手机优化 */
    @media (max-width: 420px) {
      .card { padding: 26px 22px; border-radius: 18px; }
      .heart { transform: scale(.9); }
    }
  </style>
</head>
<body>
  <canvas id="fireworks"></canvas>

  <div class="center">
    <div class="card">
      <div class="heart" aria-hidden="true"></div>
      <h1>邓悠然，我喜欢你</h1>
      <h2>今夜的烟花，都是我为你点亮的星河</h2>

      <p class="message" id="message">
        认识你之后，许多平凡的瞬间都变得闪闪发光。
        我想把最浪漫的光，写进你的名字里：
        邓悠然——愿你的笑，永远被温柔照亮。
      </p>

      <div class="btns">
        <button class="btn btn-primary" id="launch">为你放烟花</button>
        <button class="btn btn-ghost" id="heartShow">心动瞬间</button>
        <button class="btn btn-ghost" id="musicToggle">轻音乐</button>
      </div>
      <p class="note">如果你愿意，我就把以后的每一个夜晚都点亮给你。</p>
      <p class="signature">—— 来自偷偷喜欢你的那个人</p>
    </div>
  </div>

  <!-- 可选：内置一段轻音乐（无外链，合成音调）。如不需要可移除 -->
  <audio id="bgm" preload="auto"></audio>

  <footer>按空格或点击“为你放烟花”触发。支持手机与电脑。</footer>

  <script>
    // =============== 简易烟花引擎（纯原生 JS） ===============
    const canvas = document.getElementById('fireworks');
    const ctx = canvas.getContext('2d', { alpha: true });
    let W, H, DPR = Math.min(window.devicePixelRatio || 1, 2);
    function resize() {
      W = canvas.width = Math.floor(window.innerWidth * DPR);
      H = canvas.height = Math.floor(window.innerHeight * DPR);
      canvas.style.width = window.innerWidth + 'px';
      canvas.style.height = window.innerHeight + 'px';
    }
    window.addEventListener('resize', resize, { passive: true });
    resize();

    const rand = (min, max) => Math.random() * (max - min) + min;
    const TAU = Math.PI * 2;

    const colors = [
      ['#ff7ab5', '#ffd1e6', '#fff0f7'],
      ['#7bdcff', '#b5f1ff', '#e6fbff'],
      ['#ffd56b', '#ffe7a6', '#fff7da'],
      ['#a8ffb1', '#d7ffd6', '#eaffea'],
    ];

    class Particle {
      constructor(x, y, angle, speed, color, fade=0.012) {
        this.x = x; this.y = y;
        this.vx = Math.cos(angle) * speed;
        this.vy = Math.sin(angle) * speed;
        this.life = 1;
        this.color = color;
        this.fade = fade;
        this.size = rand(1.2, 2.6);
        this.gravity = rand(0.015, 0.03);
        this.air = rand(0.985, 0.994);
        this.spark = Math.random() < 0.22;
      }
      step() {
        this.vx *= this.air;
        this.vy = this.vy * this.air + this.gravity;
        this.x += this.vx;
        this.y += this.vy;
        this.life -= this.fade;
        return this.life > 0;
      }
      draw(ctx) {
        ctx.globalAlpha = Math.max(this.life, 0) * (this.spark ? 1 : 0.85);
        ctx.fillStyle = this.color;
        ctx.beginPath();
        ctx.arc(this.x, this.y, this.size, 0, TAU);
        ctx.fill();
      }
    }

    class Firework {
      constructor(x, y, burst=140, palette=colors[Math.random()*colors.length|0]) {
        this.particles = [];
        this.trail = [];
        const baseSpeed = rand(4.6, 7.0) * DPR;
        for (let i = 0; i < burst; i++) {
          const angle = i / burst * TAU + rand(-0.02, 0.02);
          const speed = baseSpeed * (0.55 + Math.random()*0.75);
          const color = palette[(Math.random()*palette.length)|0];
          this.particles.push(new Particle(x, y, angle, speed, color));
        }
        // 额外：心形分布
        if (Math.random() < 0.35) {
          const heartPalette = ['#ff6bb3','#ff3e9e','#ff0066'];
          const n = 180;
          for (let t = 0; t < n; t++) {
            const theta = t / n * TAU;
            const hx = 16 * Math.pow(Math.sin(theta), 3);
            const hy = 13 * Math.cos(theta) - 5 * Math.cos(2*theta) - 2*Math.cos(3*theta) - Math.cos(4*theta);
            const scale = 6.2 * DPR;
            const xh = x + hx * scale;
            const yh = y - hy * scale;
            const angle = Math.atan2(yh - y, xh - x);
            const p = new Particle(x, y, angle, rand(3.0, 4.2)*DPR, heartPalette[(Math.random()*heartPalette.length)|0], 0.010);
            p.x = xh; p.y = yh; // 直接心形位置
            p.vx *= 0.25; p.vy *= 0.25;
            p.size = rand(1.4, 2.0);
            this.particles.push(p);
          }
        }
      }
      step(ctx) {
        this.trail.push(this.particles[0]?.x|0);
        for (let i = this.particles.length - 1; i >= 0; i--) {
          const p = this.particles[i];
          if (!p.step()) this.particles.splice(i, 1);
          else p.draw(ctx);
        }
        return this.particles.length > 0;
      }
    }

    const fireworks = [];
    let lastLaunch = 0;

    function launchFirework(cx, cy) {
      fireworks.push(new Firework(cx, cy, rand(120, 200)));
      // 辅助闪光
      for (let i = 0; i < 24; i++) {
        const angle = Math.random() * TAU;
        const p = new Particle(cx, cy, angle, rand(2.2, 3.6)*DPR, 'rgba(255,255,255,0.9)', 0.02);
        p.size = rand(1.8, 3.0);
        fireworks.push({ particles: [p], step: fwStepOnce });
      }
    }
    function fwStepOnce(ctx) {
      const p = this.particles[0];
      if (p && p.step()) p.draw(ctx);
      return p && p.life > 0;
    }

    function animate(ts) {
      ctx.globalCompositeOperation = 'source-over';
      ctx.fillStyle = 'rgba(6, 8, 18, 0.24)';
      ctx.fillRect(0, 0, W, H);

      ctx.globalCompositeOperation = 'lighter';
      for (let i = fireworks.length - 1; i >= 0; i--) {
        if (!fireworks[i].step(ctx)) fireworks.splice(i, 1);
      }
      requestAnimationFrame(animate);
    }
    requestAnimationFrame(animate);

    // 交互：点击或空格触发烟花
    function toCanvasPos(clientX, clientY) {
      const rect = canvas.getBoundingClientRect();
      return [(clientX - rect.left) * DPR, (clientY - rect.top) * DPR];
    }
    window.addEventListener('pointerdown', (e) => {
      const [x, y] = toCanvasPos(e.clientX, e.clientY);
      launchFirework(x, y);
    });
    window.addEventListener('keydown', (e) => {
      if (e.code === 'Space') {
        const x = rand(W*0.2, W*0.8);
        const y = rand(H*0.2, H*0.55);
        launchFirework(x, y);
      }
    });

    // 按钮：连续烟花与心形展示
    const launchBtn = document.getElementById('launch');
    const heartBtn = document.getElementById('heartShow');
    launchBtn.addEventListener('click', () => {
      const seq = 8;
      for (let i = 0; i < seq; i++) {
        setTimeout(() => launchFirework(rand(W*0.18, W*0.82), rand(H*0.18, H*0.6)), i*260);
      }
    });
    heartBtn.addEventListener('click', () => {
      const cx = W/2, cy = H*0.42;
      for (let i = 0; i < 3; i++) setTimeout(() => launchFirework(cx, cy), i*400);
      // 打字机效果
      typeLove(`邓悠然，我喜欢你。愿将温柔和热烈，统统给你。`);
    });

    // 打字机文字
    const msg = document.getElementById('message');
    function typeLove(text, speed=42) {
      msg.textContent = '';
      let i = 0;
      const timer = setInterval(() => {
        msg.textContent += text[i++];
        if (i >= text.length) clearInterval(timer);
      }, speed);
    }

    // 轻音乐（WebAudio 简谱合成）
    const musicBtn = document.getElementById('musicToggle');
    const audioEl = document.getElementById('bgm');
    let audioCtx, playing = false;
    const notes = [
      // 简单温柔的旋律，单位秒（时值），A4=440Hz
      { f: 440, d: .42 }, { f: 494, d: .28 }, { f: 523, d: .36 }, { f: 587, d: .54 },
      { f: 659, d: .42 }, { f: 587, d: .34 }, { f: 523, d: .42 }, { f: 494, d: .36 },
      { f: 523, d: .66 }, { f: 587, d: .46 }, { f: 659, d: .72 }, { f: 698, d: .44 },
      { f: 659, d: .34 }, { f: 587, d: .44 }, { f: 523, d: .9 },
    ];
    async function playMelody() {
      if (!audioCtx) audioCtx = new (window.AudioContext || window.webkitAudioContext)();
      const master = audioCtx.createGain();
      master.gain.value = 0.06; // 轻音量
      master.connect(audioCtx.destination);
      let t = audioCtx.currentTime;
      for (const { f, d } of notes) {
        const osc = audioCtx.createOscillator();
        const gain = audioCtx.createGain();
        osc.type = 'sine';
        osc.frequency.value = f;
        const attack = 0.02, release = 0.12;
        gain.gain.setValueAtTime(0, t);
        gain.gain.linearRampToValueAtTime(0.9, t + attack);
        gain.gain.linearRampToValueAtTime(0.0, t + d - release);
        gain.gain.linearRampToValueAtTime(0.0, t + d);
        osc.connect(gain); gain.connect(master);
        osc.start(t);
        osc.stop(t + d);
        t += d + 0.05;
      }
      playing = true;
      setTimeout(() => playing = false, (notes.reduce((s,n)=>s+n.d,0) + 0.8) * 1000);
    }
    musicBtn.addEventListener('click', async () => {
      if (!playing) await playMelody();
    });

    // 首次进入的小惊喜：自动播 3 轮烟花
    window.addEventListener('load', () => {
      const cx = W/2, cy = H*0.45;
      for (let i = 0; i < 3; i++) setTimeout(() => launchFirework(cx + rand(-W*0.1, W*0.1), cy + rand(-H*0.05, H*0.05)), i*500);
    });
  </script>
</body>
</html>
