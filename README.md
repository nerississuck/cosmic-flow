<!DOCTYPE html>
<html lang="en" class="h-full dark">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Cosmic Flow - Interactive Particle Sandbox</title>
  
  <!-- Tailwind CSS CDN -->
  <script src="https://cdn.tailwindcss.com"></script>
  
  <!-- Google Fonts: Outfit & Inter -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&family=Outfit:wght@400;600;800&display=swap" rel="stylesheet">
  
  <script>
    tailwind.config = {
      darkMode: 'class',
      theme: {
        extend: {
          fontFamily: {
            sans: ['Inter', 'sans-serif'],
            display: ['Outfit', 'sans-serif'],
          },
          colors: {
            cosmic: {
              900: '#080812',
              800: '#0f0f23',
              700: '#1a1a3a',
            }
          }
        }
      }
    }
  </script>

  <style>
    /* Custom Scrollbar for Controls UI */
    ::-webkit-scrollbar {
      width: 6px;
    }
    ::-webkit-scrollbar-track {
      background: rgba(15, 15, 35, 0.5);
      border-radius: 8px;
    }
    ::-webkit-scrollbar-thumb {
      background: rgba(255, 255, 255, 0.2);
      border-radius: 8px;
    }
    ::-webkit-scrollbar-thumb:hover {
      background: rgba(255, 255, 255, 0.4);
    }
    
    /* Range Input Customization */
    input[type=range] {
      -webkit-appearance: none;
      background: rgba(255, 255, 255, 0.1);
      border-radius: 6px;
      height: 6px;
    }
    input[type=range]::-webkit-slider-thumb {
      -webkit-appearance: none;
      height: 16px;
      width: 16px;
      border-radius: 50%;
      background: #00f3ff;
      box-shadow: 0 0 10px #00f3ff;
      cursor: pointer;
      transition: transform 0.1s ease;
    }
    input[type=range]::-webkit-slider-thumb:hover {
      transform: scale(1.2);
    }

    /* Glassmorphism effect */
    .glass-panel {
      background: rgba(15, 15, 35, 0.65);
      backdrop-filter: blur(16px);
      -webkit-backdrop-filter: blur(16px);
      border: 1px solid rgba(255, 255, 255, 0.12);
    }

    .glass-btn {
      background: rgba(255, 255, 255, 0.05);
      backdrop-filter: blur(8px);
      border: 1px solid rgba(255, 255, 255, 0.1);
      transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
    }
    .glass-btn:hover {
      background: rgba(255, 255, 255, 0.15);
      border-color: rgba(255, 255, 255, 0.3);
      transform: translateY(-1px);
    }
    .glass-btn.active {
      background: linear-gradient(135deg, rgba(0, 243, 255, 0.25), rgba(255, 0, 127, 0.25));
      border-color: rgba(0, 243, 255, 0.5);
      box-shadow: 0 0 15px rgba(0, 243, 255, 0.3);
    }
  </style>
</head>
<body class="bg-cosmic-900 text-slate-100 h-full overflow-hidden select-none font-sans">

  <!-- Interactive Canvas -->
  <canvas id="canvas" class="fixed inset-0 w-full h-full cursor-crosshair touch-none z-0"></canvas>

  <!-- Top Bar Controls / Header -->
  <header class="fixed top-4 left-4 right-4 z-20 flex justify-between items-center pointer-events-none">
    <!-- Brand Title -->
    <div class="glass-panel px-4 py-2.5 rounded-2xl flex items-center gap-3 pointer-events-auto shadow-2xl">
      <div class="w-3 h-3 rounded-full bg-cyan-400 animate-ping"></div>
      <div>
        <h1 class="font-display font-bold text-lg leading-none bg-gradient-to-r from-cyan-400 via-pink-500 to-purple-400 bg-clip-text text-transparent">
          COSMIC FLOW
        </h1>
        <p class="text-[10px] text-slate-400 font-medium tracking-wider uppercase mt-0.5">Physics Particle Sandbox</p>
      </div>
    </div>

    <!-- Stats & Quick Actions -->
    <div class="flex items-center gap-2 pointer-events-auto">
      <div class="glass-panel px-3 py-1.5 rounded-xl hidden sm:flex items-center gap-3 text-xs font-mono">
        <div><span class="text-slate-400">FPS:</span> <span id="fpsVal" class="text-cyan-400 font-bold">60</span></div>
        <div class="w-px h-3 bg-white/20"></div>
        <div><span class="text-slate-400">PARTICLES:</span> <span id="countVal" class="text-pink-400 font-bold">5000</span></div>
      </div>

      <button id="audioBtn" class="glass-btn p-2.5 rounded-xl text-slate-300 hover:text-white" title="Toggle Sound FX">
        <svg id="audioOnIcon" class="w-5 h-5 hidden" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15.536 8.464a5 5 0 010 7.072m2.828-9.9a9 9 0 010 12.728M5.586 15H4a1 1 0 01-1-1v-4a1 1 0 011-1h1.586l4.707-4.707C10.923 3.663 12 4.109 12 5v14c0 .891-1.077 1.337-1.707.707L5.586 15z"/></svg>
        <svg id="audioOffIcon" class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5.586 15H4a1 1 0 01-1-1v-4a1 1 0 011-1h1.586l4.707-4.707C10.923 3.663 12 4.109 12 5v14c0 .891-1.077 1.337-1.707.707L5.586 15z" /><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 14l2-2m0 0l2-2m-2 2l-2-2m2 2l2 2"/></svg>
      </button>

      <button id="snapBtn" class="glass-btn p-2.5 rounded-xl text-slate-300 hover:text-white" title="Capture Screenshot">
        <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 9a2 2 0 012-2h0.93a2 2 0 001.664-.89l.812-1.22A2 2 0 0110.07 4h3.86a2 2 0 011.664.89l.812 1.22A2 2 0 0018.07 7H19a2 2 0 012 2v9a2 2 0 01-2 2H5a2 2 0 01-2-2V9z"/><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 13a3 3 0 11-6 0 3 3 0 016 0z"/></svg>
      </button>

      <button id="toggleUiBtn" class="glass-btn p-2.5 rounded-xl text-slate-300 hover:text-white" title="Hide UI (Key: H)">
        <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13.875 18.825A10.05 10.05 0 0112 19c-4.478 0-8.268-2.943-9.543-7a9.97 9.97 0 011.563-3.029m5.858-5.908a10.043 10.043 0 012.122-.063c4.478 0 8.268 2.943 9.542 7a10.025 10.025 0 01-4.132 5.411m0 0L21 21M3 3l18 18"/></svg>
      </button>
    </div>
  </header>

  <!-- Floating Main Control Panel -->
  <main id="uiPanel" class="fixed right-4 top-20 bottom-4 w-80 z-20 flex flex-col gap-4 overflow-y-auto pr-1 transition-all duration-300 transform translate-x-0">
    
    <!-- Section 1: Physics Modes -->
    <div class="glass-panel p-4 rounded-2xl flex flex-col gap-3">
      <h2 class="text-xs font-bold text-slate-400 tracking-wider uppercase flex items-center justify-between">
        <span>Physics Mode</span>
        <span class="text-[10px] text-cyan-400/80 font-normal">[Hotkeys: 1-5]</span>
      </h2>
      <div class="grid grid-cols-1 gap-1.5">
        <button data-mode="blackhole" class="mode-btn glass-btn active px-3 py-2 rounded-xl text-xs font-medium text-left flex items-center gap-2">
          <span class="w-2 h-2 rounded-full bg-cyan-400"></span> Black Hole (Attractor)
        </button>
        <button data-mode="vortex" class="mode-btn glass-btn px-3 py-2 rounded-xl text-xs font-medium text-left flex items-center gap-2">
          <span class="w-2 h-2 rounded-full bg-purple-400"></span> Cosmic Vortex
        </button>
        <button data-mode="repeller" class="mode-btn glass-btn px-3 py-2 rounded-xl text-xs font-medium text-left flex items-center gap-2">
          <span class="w-2 h-2 rounded-full bg-pink-400"></span> Force Field (Repeller)
        </button>
        <button data-mode="supernova" class="mode-btn glass-btn px-3 py-2 rounded-xl text-xs font-medium text-left flex items-center gap-2">
          <span class="w-2 h-2 rounded-full bg-amber-400"></span> Supernova Explosions
        </button>
        <button data-mode="wave" class="mode-btn glass-btn px-3 py-2 rounded-xl text-xs font-medium text-left flex items-center gap-2">
          <span class="w-2 h-2 rounded-full bg-emerald-400"></span> Cyber Wave Simulation
        </button>
      </div>
    </div>

    <!-- Section 2: Presets -->
    <div class="glass-panel p-4 rounded-2xl flex flex-col gap-3">
      <h2 class="text-xs font-bold text-slate-400 tracking-wider uppercase">Cosmic Presets</h2>
      <div class="grid grid-cols-2 gap-2">
        <button id="presetGalactic" class="glass-btn p-2 rounded-xl text-xs font-medium text-slate-200 text-center hover:text-cyan-300">
          Galactic Core
        </button>
        <button id="presetShockwave" class="glass-btn p-2 rounded-xl text-xs font-medium text-slate-200 text-center hover:text-pink-300">
          Supernova
        </button>
        <button id="presetPortal" class="glass-btn p-2 rounded-xl text-xs font-medium text-slate-200 text-center hover:text-purple-300">
          Wormhole
        </button>
        <button id="presetMatrix" class="glass-btn p-2 rounded-xl text-xs font-medium text-slate-200 text-center hover:text-emerald-300">
          Matrix Rain
        </button>
      </div>
    </div>

    <!-- Section 3: Fine Tuning Controls -->
    <div class="glass-panel p-4 rounded-2xl flex flex-col gap-4">
      <h2 class="text-xs font-bold text-slate-400 tracking-wider uppercase">Simulation Controls</h2>
      
      <!-- Particle Count -->
      <div class="flex flex-col gap-1.5">
        <div class="flex justify-between text-xs">
          <span class="text-slate-300 font-medium">Particle Count</span>
          <span id="sliderCountText" class="text-cyan-400 font-mono">5,000</span>
        </div>
        <input type="range" id="sliderCount" min="1000" max="15000" step="500" value="5000">
      </div>

      <!-- Gravity Strength -->
      <div class="flex flex-col gap-1.5">
        <div class="flex justify-between text-xs">
          <span class="text-slate-300 font-medium">Force Gravity</span>
          <span id="sliderGravityText" class="text-cyan-400 font-mono">1.2</span>
        </div>
        <input type="range" id="sliderGravity" min="0.1" max="4.0" step="0.1" value="1.2">
      </div>

      <!-- Damping / Friction -->
      <div class="flex flex-col gap-1.5">
        <div class="flex justify-between text-xs">
          <span class="text-slate-300 font-medium">Friction (Damping)</span>
          <span id="sliderFrictionText" class="text-cyan-400 font-mono">0.96</span>
        </div>
        <input type="range" id="sliderFriction" min="0.80" max="0.99" step="0.01" value="0.96">
      </div>

      <!-- Trail Length -->
      <div class="flex flex-col gap-1.5">
        <div class="flex justify-between text-xs">
          <span class="text-slate-300 font-medium">Glow Trail Persistence</span>
          <span id="sliderTrailText" class="text-cyan-400 font-mono">0.15</span>
        </div>
        <input type="range" id="sliderTrail" min="0.02" max="0.5" step="0.01" value="0.15">
      </div>

      <!-- Particle Size -->
      <div class="flex flex-col gap-1.5">
        <div class="flex justify-between text-xs">
          <span class="text-slate-300 font-medium">Particle Size</span>
          <span id="sliderSizeText" class="text-cyan-400 font-mono">1.8</span>
        </div>
        <input type="range" id="sliderSize" min="0.5" max="5.0" step="0.1" value="1.8">
      </div>
    </div>

    <!-- Section 4: Color Themes -->
    <div class="glass-panel p-4 rounded-2xl flex flex-col gap-3">
      <h2 class="text-xs font-bold text-slate-400 tracking-wider uppercase">Color Palettes</h2>
      <div class="grid grid-cols-5 gap-2">
        <button data-theme="cyberpunk" class="theme-btn h-8 rounded-xl bg-gradient-to-tr from-cyan-400 to-pink-500 ring-2 ring-white/50" title="Cyberpunk Neon"></button>
        <button data-theme="synthwave" class="theme-btn h-8 rounded-xl bg-gradient-to-tr from-amber-400 to-purple-600" title="Synthwave Gold"></button>
        <button data-theme="nebula" class="theme-btn h-8 rounded-xl bg-gradient-to-tr from-blue-500 to-indigo-600" title="Deep Nebula"></button>
        <button data-theme="matrix" class="theme-btn h-8 rounded-xl bg-gradient-to-tr from-emerald-400 to-teal-700" title="Matrix Emerald"></button>
        <button data-theme="rainbow" class="theme-btn h-8 rounded-xl bg-gradient-to-tr from-red-500 via-green-400 to-blue-500" title="Dynamic Spectrum"></button>
      </div>
    </div>

    <!-- Clear & Reset Button -->
    <button id="resetCanvasBtn" class="glass-btn p-3 rounded-2xl text-xs font-semibold text-rose-400 hover:text-rose-300 flex items-center justify-center gap-2">
      <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"/></svg>
      Reset Simulation
    </button>
  </main>

  <!-- Collapsed Show UI Button (Hidden by default) -->
  <button id="showUiBtn" class="fixed right-4 top-4 z-30 glass-panel p-3 rounded-2xl text-slate-300 hover:text-white hidden shadow-2xl pointer-events-auto">
    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10.325 4.317c.426-1.756 2.924-1.756 3.35 0a1.724 1.724 0 002.573 1.066c1.543-.94 3.31.826 2.37 2.37a1.724 1.724 0 001.065 2.572c1.756.426 1.756 2.924 0 3.35a1.724 1.724 0 00-1.066 2.573c.94 1.543-.826 3.31-2.37 2.37a1.724 1.724 0 00-2.572 1.065c-.426 1.756-2.924 1.756-3.35 0a1.724 1.724 0 00-2.573-1.066c-1.543.94-3.31-.826-2.37-2.37a1.724 1.724 0 00-1.065-2.572c-1.756-.426-1.756-2.924 0-3.35a1.724 1.724 0 001.066-2.573c-.94-1.543.826-3.31 2.37-2.37.996.608 2.296.07 2.572-1.065z"/><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z"/></svg>
  </button>

  <!-- Interactive Simulation JavaScript Engine -->
  <script>
    // Canvas & Context Setup
    const canvas = document.getElementById('canvas');
    const ctx = canvas.getContext('2d');

    // UI Elements
    const fpsVal = document.getElementById('fpsVal');
    const countVal = document.getElementById('countVal');
    const uiPanel = document.getElementById('uiPanel');
    const toggleUiBtn = document.getElementById('toggleUiBtn');
    const showUiBtn = document.getElementById('showUiBtn');
    const audioBtn = document.getElementById('audioBtn');
    const audioOnIcon = document.getElementById('audioOnIcon');
    const audioOffIcon = document.getElementById('audioOffIcon');
    const snapBtn = document.getElementById('snapBtn');
    const resetCanvasBtn = document.getElementById('resetCanvasBtn');

    // Controls
    const sliderCount = document.getElementById('sliderCount');
    const sliderGravity = document.getElementById('sliderGravity');
    const sliderFriction = document.getElementById('sliderFriction');
    const sliderTrail = document.getElementById('sliderTrail');
    const sliderSize = document.getElementById('sliderSize');

    // Config & State Variables
    let width = canvas.width = window.innerWidth;
    let height = canvas.height = window.innerHeight;
    
    let particleCount = parseInt(sliderCount.value);
    let gravity = parseFloat(sliderGravity.value);
    let friction = parseFloat(sliderFriction.value);
    let trailAlpha = parseFloat(sliderTrail.value);
    let particleBaseSize = parseFloat(sliderSize.value);

    let activeMode = 'blackhole'; // 'blackhole', 'vortex', 'repeller', 'supernova', 'wave'
    let currentTheme = 'cyberpunk';
    let soundEnabled = false;

    // Mouse & Touch Pointer Tracking
    const mouse = {
      x: width / 2,
      y: height / 2,
      px: width / 2,
      py: height / 2,
      vx: 0,
      vy: 0,
      down: false,
      active: false
    };

    // Color Palettes Definition
    const themes = {
      cyberpunk: ['#00f3ff', '#ff007f', '#9d00ff', '#ffffff'],
      synthwave: ['#ff7700', '#ffdd00', '#ff0055', '#d400ff'],
      nebula: ['#00b4d8', '#9d4edd', '#3a0ca3', '#4cc9f0'],
      matrix: ['#00ff66', '#00f5d4', '#05f140', '#ffffff'],
      rainbow: ['#ff0000', '#ff7f00', '#ffff00', '#00ff00', '#0000ff', '#8b00ff']
    };

    // Web Audio Synthesizer Setup
    let audioCtx = null;
    function initAudio() {
      if (!audioCtx) {
        audioCtx = new (window.AudioContext || window.webkitAudioContext)();
      }
    }

    function playSynthesizedSound(freq, type = 'sine', duration = 0.15, vol = 0.05) {
      if (!soundEnabled || !audioCtx) return;
      try {
        const osc = audioCtx.createOscillator();
        const gain = audioCtx.createGain();
        osc.type = type;
        osc.frequency.setValueAtTime(freq, audioCtx.currentTime);
        gain.gain.setValueAtTime(vol, audioCtx.currentTime);
        gain.gain.exponentialRampToValueAtTime(0.0001, audioCtx.currentTime + duration);
        osc.connect(gain);
        gain.connect(audioCtx.destination);
        osc.start();
        osc.stop(audioCtx.currentTime + duration);
      } catch (e) {
        // Audio error handle fallback
      }
    }

    // Particle Class
    class Particle {
      constructor() {
        this.reset(true);
      }

      reset(initial = false) {
        this.x = initial ? Math.random() * width : mouse.x + (Math.random() - 0.5) * 50;
        this.y = initial ? Math.random() * height : mouse.y + (Math.random() - 0.5) * 50;
        this.vx = (Math.random() - 0.5) * 2;
        this.vy = (Math.random() - 0.5) * 2;
        this.size = particleBaseSize * (0.6 + Math.random() * 0.8);
        this.assignColor();
      }

      assignColor() {
        const palette = themes[currentTheme];
        this.color = palette[Math.floor(Math.random() * palette.length)];
      }

      update() {
        const dx = mouse.x - this.x;
        const dy = mouse.y - this.y;
        const distSq = dx * dx + dy * dy;
        const dist = Math.sqrt(distSq) + 0.1;

        // Apply Forces Based on Active Physics Mode
        if (mouse.active || activeMode === 'wave') {
          switch (activeMode) {
            case 'blackhole': {
              const force = (gravity * 1500) / distSq;
              this.vx += (dx / dist) * force;
              this.vy += (dy / dist) * force;
              break;
            }
            case 'vortex': {
              const force = (gravity * 1200) / distSq;
              const angle = Math.atan2(dy, dx) + Math.PI / 2;
              this.vx += Math.cos(angle) * force * 12 + (dx / dist) * force;
              this.vy += Math.sin(angle) * force * 12 + (dy / dist) * force;
              break;
            }
            case 'repeller': {
              if (dist < 250) {
                const force = (gravity * 3000) / distSq;
                this.vx -= (dx / dist) * force;
                this.vy -= (dy / dist) * force;
              }
              break;
            }
            case 'wave': {
              const speed = Math.sqrt(mouse.vx * mouse.vx + mouse.vy * mouse.vy);
              if (dist < 180) {
                this.vx += mouse.vx * 0.15 * (1 - dist / 180);
                this.vy += mouse.vy * 0.15 * (1 - dist / 180);
              }
              break;
            }
          }
        }

        // Apply Friction & Velocity
        this.vx *= friction;
        this.vy *= friction;
        this.x += this.vx;
        this.y += this.vy;

        // Canvas Boundary Bounce / Wrap Around
        if (this.x < 0) { this.x = 0; this.vx *= -1; }
        if (this.x > width) { this.x = width; this.vx *= -1; }
        if (this.y < 0) { this.y = 0; this.vy *= -1; }
        if (this.y > height) { this.y = height; this.vy *= -1; }
      }

      draw() {
        ctx.fillStyle = this.color;
        ctx.beginPath();
        ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
        ctx.fill();
      }
    }

    // Particle Pool Management
    let particles = [];
    function initParticles() {
      particles = [];
      for (let i = 0; i < particleCount; i++) {
        particles.push(new Particle());
      }
      countVal.innerText = particleCount.toLocaleString();
    }

    // Trigger Supernova Explosive Burst
    function triggerSupernova(x, y) {
      const burstCount = Math.min(particles.length, 300);
      playSynthesizedSound(120, 'sawtooth', 0.4, 0.15);
      
      for (let i = 0; i < burstCount; i++) {
        const p = particles[i % particles.length];
        p.x = x;
        p.y = y;
        const angle = Math.random() * Math.PI * 2;
        const speed = (2 + Math.random() * 15) * gravity;
        p.vx = Math.cos(angle) * speed;
        p.vy = Math.sin(angle) * speed;
      }
    }

    // Main Rendering & Animation Loop
    let lastTime = performance.now();
    let frameCount = 0;

    function animate(currentTime) {
      // Calculate FPS
      frameCount++;
      if (currentTime - lastTime >= 1000) {
        fpsVal.innerText = frameCount;
        frameCount = 0;
        lastTime = currentTime;
      }

      // Smooth Trail Clearing Effect
      ctx.globalCompositeOperation = 'source-over';
      ctx.fillStyle = `rgba(8, 8, 18, ${trailAlpha})`;
      ctx.fillRect(0, 0, width, height);

      // Blending mode for neon glowing visuals
      ctx.globalCompositeOperation = 'lighter';

      // Update & Render Particles
      for (let i = 0; i < particles.length; i++) {
        particles[i].update();
        particles[i].draw();
      }

      // Audio pitch interaction during drag
      if (soundEnabled && mouse.active && frameCount % 6 === 0) {
        const pitch = 200 + (mouse.y / height) * 600;
        playSynthesizedSound(pitch, 'sine', 0.1, 0.02);
      }

      requestAnimationFrame(animate);
    }

    // Event Listeners: Mouse & Touch Controls
    window.addEventListener('resize', () => {
      width = canvas.width = window.innerWidth;
      height = canvas.height = window.innerHeight;
    });

    function updatePointerPos(e) {
      mouse.px = mouse.x;
      mouse.py = mouse.y;
      
      if (e.touches && e.touches.length > 0) {
        mouse.x = e.touches[0].clientX;
        mouse.y = e.touches[0].clientY;
      } else {
        mouse.x = e.clientX;
        mouse.y = e.clientY;
      }

      mouse.vx = mouse.x - mouse.px;
      mouse.vy = mouse.y - mouse.py;
    }

    canvas.addEventListener('mousemove', (e) => {
      updatePointerPos(e);
      mouse.active = true;
    });

    canvas.addEventListener('mousedown', (e) => {
      initAudio();
      mouse.down = true;
      mouse.active = true;
      updatePointerPos(e);

      if (activeMode === 'supernova') {
        triggerSupernova(mouse.x, mouse.y);
      }
    });

    window.addEventListener('mouseup', () => {
      mouse.down = false;
    });

    canvas.addEventListener('touchstart', (e) => {
      initAudio();
      mouse.active = true;
      mouse.down = true;
      updatePointerPos(e);
      if (activeMode === 'supernova') {
        triggerSupernova(mouse.x, mouse.y);
      }
    });

    canvas.addEventListener('touchmove', (e) => {
      updatePointerPos(e);
    });

    canvas.addEventListener('touchend', () => {
      mouse.active = false;
      mouse.down = false;
    });

    // Control Panel Interactions
    // Mode Buttons
    const modeButtons = document.querySelectorAll('.mode-btn');
    modeButtons.forEach(btn => {
      btn.addEventListener('click', () => {
        modeButtons.forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
        activeMode = btn.dataset.mode;
      });
    });

    // Theme Buttons
    const themeButtons = document.querySelectorAll('.theme-btn');
    themeButtons.forEach(btn => {
      btn.addEventListener('click', () => {
        currentTheme = btn.dataset.theme;
        particles.forEach(p => p.assignColor());
      });
    });

    // Real-time Sliders
    sliderCount.addEventListener('input', (e) => {
      particleCount = parseInt(e.target.value);
      document.getElementById('sliderCountText').innerText = particleCount.toLocaleString();
      initParticles();
    });

    sliderGravity.addEventListener('input', (e) => {
      gravity = parseFloat(e.target.value);
      document.getElementById('sliderGravityText').innerText = gravity.toFixed(1);
    });

    sliderFriction.addEventListener('input', (e) => {
      friction = parseFloat(e.target.value);
      document.getElementById('sliderFrictionText').innerText = friction.toFixed(2);
    });

    sliderTrail.addEventListener('input', (e) => {
      trailAlpha = parseFloat(e.target.value);
      document.getElementById('sliderTrailText').innerText = trailAlpha.toFixed(2);
    });

    sliderSize.addEventListener('input', (e) => {
      particleBaseSize = parseFloat(e.target.value);
      document.getElementById('sliderSizeText').innerText = particleBaseSize.toFixed(1);
      particles.forEach(p => p.size = particleBaseSize * (0.6 + Math.random() * 0.8));
    });

    // Cosmic Presets Handler
    document.getElementById('presetGalactic').addEventListener('click', () => {
      setPreset('blackhole', 'cyberpunk', 8000, 1.8, 0.96, 0.12, 1.5);
    });
    document.getElementById('presetShockwave').addEventListener('click', () => {
      setPreset('supernova', 'synthwave', 6000, 2.5, 0.94, 0.25, 2.2);
    });
    document.getElementById('presetPortal').addEventListener('click', () => {
      setPreset('vortex', 'nebula', 10000, 2.0, 0.97, 0.08, 1.2);
    });
    document.getElementById('presetMatrix').addEventListener('click', () => {
      setPreset('wave', 'matrix', 7000, 1.0, 0.92, 0.20, 2.0);
    });

    function setPreset(mode, theme, count, grav, fric, trail, size) {
      activeMode = mode;
      currentTheme = theme;
      
      sliderCount.value = count;
      sliderGravity.value = grav;
      sliderFriction.value = fric;
      sliderTrail.value = trail;
      sliderSize.value = size;

      particleCount = count;
      gravity = grav;
      friction = fric;
      trailAlpha = trail;
      particleBaseSize = size;

      document.getElementById('sliderCountText').innerText = count.toLocaleString();
      document.getElementById('sliderGravityText').innerText = grav.toFixed(1);
      document.getElementById('sliderFrictionText').innerText = fric.toFixed(2);
      document.getElementById('sliderTrailText').innerText = trail.toFixed(2);
      document.getElementById('sliderSizeText').innerText = size.toFixed(1);

      // Update UI button highlights
      modeButtons.forEach(b => b.classList.toggle('active', b.dataset.mode === mode));
      
      initParticles();
    }

    // Toggle Sound FX
    audioBtn.addEventListener('click', () => {
      initAudio();
      soundEnabled = !soundEnabled;
      audioOnIcon.classList.toggle('hidden', !soundEnabled);
      audioOffIcon.classList.toggle('hidden', soundEnabled);
    });

    // Capture Canvas PNG Screenshot
    snapBtn.addEventListener('click', () => {
      const link = document.createElement('a');
      link.download = `cosmic-flow-${Date.now()}.png`;
      link.href = canvas.toDataURL('image/png');
      link.click();
    });

    // Hide / Show UI Controls
    function toggleUI(show) {
      if (show) {
        uiPanel.classList.remove('translate-x-96', 'opacity-0', 'pointer-events-none');
        showUiBtn.classList.add('hidden');
      } else {
        uiPanel.classList.add('translate-x-96', 'opacity-0', 'pointer-events-none');
        showUiBtn.classList.remove('hidden');
      }
    }

    toggleUiBtn.addEventListener('click', () => toggleUI(false));
    showUiBtn.addEventListener('click', () => toggleUI(true));

    // Keyboard Hotkeys
    window.addEventListener('keydown', (e) => {
      if (e.key.toLowerCase() === 'h') {
        const isHidden = uiPanel.classList.contains('translate-x-96');
        toggleUI(isHidden);
      } else if (e.key === '1') {
        modeButtons[0].click();
      } else if (e.key === '2') {
        modeButtons[1].click();
      } else if (e.key === '3') {
        modeButtons[2].click();
      } else if (e.key === '4') {
        modeButtons[3].click();
      } else if (e.key === '5') {
        modeButtons[4].click();
      } else if (e.code === 'Space') {
        triggerSupernova(mouse.x, mouse.y);
      }
    });

    resetCanvasBtn.addEventListener('click', () => initParticles());

    // Boot Application Engine
    initParticles();
    requestAnimationFrame(animate);
  </script>
</body>
</html>