<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Worm Zone - Snake.io Style Game</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" rel="stylesheet">
    <style>
        body, html {
            margin: 0;
            padding: 0;
            width: 100%;
            height: 100%;
            overflow: hidden;
            background-color: #0f172a;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            user-select: none;
            touch-action: none;
        }

        #game-canvas {
            display: block;
            width: 100vw;
            height: 100vh;
            cursor: crosshair;
        }

        .glass {
            background: rgba(15, 23, 42, 0.8);
            backdrop-filter: blur(14px);
            -webkit-backdrop-filter: blur(14px);
            border: 1px solid rgba(255, 255, 255, 0.15);
        }

        .glow-button {
            box-shadow: 0 0 15px rgba(245, 158, 11, 0.5);
            transition: all 0.2s ease-in-out;
        }

        .glow-button:hover {
            box-shadow: 0 0 25px rgba(245, 158, 11, 0.8);
            transform: translateY(-2px);
        }

        .skin-card {
            transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1);
        }
        .skin-card.selected {
            border-color: #f59e0b;
            box-shadow: 0 0 15px rgba(245, 158, 11, 0.5);
            transform: scale(1.05);
        }

        @keyframes pulse {
            0%, 100% { opacity: 1; }
            50% { opacity: 0.7; }
        }

        .boosting {
            animation: pulse 0.5s infinite;
        }

        /* Custom Scrollbar for Settings */
        ::-webkit-scrollbar {
            width: 6px;
        }
        ::-webkit-scrollbar-track {
            background: rgba(15, 23, 42, 0.5);
        }
        ::-webkit-scrollbar-thumb {
            background: #f59e0b;
            border-radius: 4px;
        }
    </style>
</head>
<body class="relative">

    <!-- Game Canvas -->
    <canvas id="game-canvas"></canvas>

    <!-- UI Overlay: HUD during Gameplay -->
    <div id="hud" class="hidden absolute inset-0 pointer-events-none flex flex-col justify-between p-4 z-10">
        <!-- Top Bar: Leaderboard & Stats & Music Controls -->
        <div class="flex justify-between items-start">
            <!-- Score & Length & Timer -->
            <div class="glass rounded-2xl p-4 text-white min-w-[160px] shadow-lg pointer-events-auto">
                <div class="text-xs uppercase tracking-wider text-amber-400 font-bold mb-1">Điểm Số</div>
                <div id="score-val" class="text-3xl font-black text-amber-300">0</div>
                <div class="text-xs text-slate-300 mt-1">Độ dài: <span id="length-val" class="font-bold">10</span>m</div>
                <div id="timer-box" class="hidden text-xs text-rose-400 mt-1 font-mono font-bold">Thời gian: <span id="timer-val">03:00</span></div>
            </div>

            <!-- In-game Audio Control & Leaderboard -->
            <div class="flex flex-col items-end gap-2">
                <!-- Leaderboard -->
                <div class="glass rounded-2xl p-4 text-white min-w-[210px] shadow-lg pointer-events-auto">
                    <div class="text-xs uppercase tracking-wider text-amber-400 font-bold mb-2 flex items-center justify-between">
                        <span class="flex items-center gap-1.5"><i class="fa-solid fa-crown text-amber-400"></i> Bảng Xếp Hạng</span>
                        <button id="hud-sound-btn" class="text-slate-300 hover:text-amber-400 text-sm transition">
                            <i id="hud-sound-icon" class="fa-solid fa-volume-high"></i>
                        </button>
                    </div>
                    <ol id="leaderboard-list" class="space-y-1 text-sm font-semibold">
                        <!-- Dynamic Leaderboard Items -->
                    </ol>
                </div>
            </div>
        </div>

        <!-- Bottom Bar: Minimap & Boost Gauge -->
        <div class="flex justify-between items-end">
            <!-- Boost Indicator -->
            <div class="glass rounded-2xl p-3 text-white pointer-events-auto flex items-center gap-3">
                <div class="text-xs font-bold uppercase text-amber-400">Tăng tốc</div>
                <div class="w-32 bg-slate-700 h-4 rounded-full overflow-hidden p-0.5 border border-amber-500/30">
                    <div id="boost-bar" class="bg-gradient-to-r from-amber-500 to-yellow-300 h-full rounded-full w-full transition-all duration-75"></div>
                </div>
                <span class="text-xs text-slate-300 font-mono">(Giữ Chuột / Space)</span>
            </div>

            <!-- Mini Map -->
            <div class="glass p-2 rounded-2xl pointer-events-auto">
                <canvas id="minimap" width="120" height="120" class="rounded-xl border border-slate-600 bg-slate-900/80"></canvas>
            </div>
        </div>
    </div>

    <!-- Main Menu Overlay -->
    <div id="start-screen" class="absolute inset-0 z-20 flex items-center justify-center p-4 bg-slate-950/85 backdrop-blur-md overflow-y-auto">
        <div class="glass rounded-3xl p-6 sm:p-8 max-w-xl w-full text-center text-white shadow-2xl border border-amber-500/20 my-auto">
            <!-- Header Logo -->
            <div class="mb-4">
                <div class="inline-flex items-center justify-center p-3 bg-amber-500/20 rounded-full mb-2 border border-amber-400/30">
                    <i class="fa-solid fa-crown text-4xl text-amber-400 drop-shadow-md"></i>
                </div>
                <h1 class="text-4xl sm:text-5xl font-extrabold tracking-wide bg-gradient-to-r from-yellow-300 via-amber-400 to-orange-500 bg-clip-text text-transparent">
                    WORM ZONE .IO
                </h1>
                <p class="text-slate-400 text-xs sm:text-sm mt-1">Săn thức ăn, bật nhạc quẩy & trở thành vua sâu!</p>
            </div>

            <!-- Player Input Form & Highscore Badge -->
            <div class="grid grid-cols-1 sm:grid-cols-2 gap-3 mb-4">
                <div>
                    <label class="block text-left text-xs font-bold text-slate-300 uppercase mb-1">Tên chú sâu:</label>
                    <input type="text" id="player-name" value="Sâu Béo" maxlength="15" 
                        class="w-full bg-slate-800/80 border border-slate-600 rounded-xl px-4 py-2 text-white text-center font-bold focus:outline-none focus:border-amber-400 focus:ring-2 focus:ring-amber-400/50 transition">
                </div>
                <div>
                    <label class="block text-left text-xs font-bold text-slate-300 uppercase mb-1">Điểm Cao Nhất:</label>
                    <div class="bg-slate-900/80 border border-slate-700 rounded-xl py-2 px-4 font-black text-amber-400 text-lg flex items-center justify-center gap-2">
                        <i class="fa-solid fa-trophy text-amber-400"></i> <span id="high-score-display">0</span>
                    </div>
                </div>
            </div>

            <!-- Skin Selector -->
            <div class="mb-4 text-left">
                <label class="block text-xs font-bold text-slate-300 uppercase mb-2">Chọn Trang Phục / Skin:</label>
                <div id="skin-grid" class="grid grid-cols-5 gap-2">
                    <!-- Skin items rendered dynamically -->
                </div>
            </div>

            <!-- Game Mode Selector -->
            <div class="mb-4 text-left">
                <label class="block text-xs font-bold text-slate-300 uppercase mb-2">Chế Độ Chơi:</label>
                <div class="grid grid-cols-2 gap-3">
                    <button id="mode-endless" class="mode-btn selected bg-slate-800/90 border-2 border-amber-400 rounded-xl p-3 text-left transition flex items-center gap-3">
                        <i class="fa-solid fa-infinity text-2xl text-amber-400"></i>
                        <div>
                            <div class="font-bold text-sm text-white">Vô Tận</div>
                            <div class="text-[10px] text-slate-400">Chơi tự do đến khi chết</div>
                        </div>
                    </button>
                    <button id="mode-time" class="mode-btn bg-slate-800/90 border-2 border-slate-700 rounded-xl p-3 text-left transition flex items-center gap-3 opacity-70 hover:opacity-100">
                        <i class="fa-solid fa-stopwatch text-2xl text-rose-400"></i>
                        <div>
                            <div class="font-bold text-sm text-white">Đấu Thời Gian</div>
                            <div class="text-[10px] text-slate-400">Thi điểm trong 3 phút</div>
                        </div>
                    </button>
                </div>
            </div>

            <!-- Audio & BGM Music Selector Panel -->
            <div class="mb-5 bg-slate-900/80 rounded-2xl p-3 border border-amber-500/30 text-left space-y-3">
                <div class="flex justify-between items-center border-b border-slate-800 pb-2">
                    <span class="text-xs font-bold text-amber-400 uppercase flex items-center gap-2">
                        <i class="fa-solid fa-music"></i> Cài Đặt Âm Thanh & Nhạc Nền
                    </span>
                    <div class="flex items-center gap-2">
                        <button id="bgm-toggle-btn" class="px-2.5 py-1 text-xs rounded-lg bg-amber-500/20 text-amber-300 border border-amber-500/30 font-bold hover:bg-amber-500/30 transition">
                            <i class="fa-solid fa-music mr-1"></i> BGM: ON
                        </button>
                        <button id="sfx-toggle-btn" class="px-2.5 py-1 text-xs rounded-lg bg-amber-500/20 text-amber-300 border border-amber-500/30 font-bold hover:bg-amber-500/30 transition">
                            <i class="fa-solid fa-volume-high mr-1"></i> SFX: ON
                        </button>
                    </div>
                </div>

                <!-- Track Dropdown -->
                <div class="grid grid-cols-1 sm:grid-cols-2 gap-3 items-center">
                    <div>
                        <label class="block text-[10px] font-bold text-slate-400 uppercase mb-1">Chọn Giai Điệu Nhạc Nền:</label>
                        <select id="bgm-track-select" class="w-full bg-slate-800 border border-slate-700 rounded-lg px-3 py-1.5 text-xs text-white font-semibold focus:outline-none focus:border-amber-400">
                            <option value="arcade">🕹️ Upbeat Arcade Synth</option>
                            <option value="edm">⚡ Energetic EDM Beat</option>
                            <option value="retro">👾 Retro 8-Bit Chiptune</option>
                            <option value="chill">☕ Chill Lo-Fi Vibe</option>
                        </select>
                    </div>
                    <!-- Volume Sliders -->
                    <div class="space-y-1">
                        <div class="flex items-center justify-between text-[10px] text-slate-300">
                            <span>Âm lượng Nhạc (BGM):</span>
                            <span id="bgm-vol-val">70%</span>
                        </div>
                        <input type="range" id="bgm-vol-slider" min="0" max="1" step="0.05" value="0.7" class="w-full accent-amber-400 h-1.5 bg-slate-700 rounded-lg cursor-pointer">
                    </div>
                </div>
            </div>

            <!-- Start Button -->
            <button id="start-btn" class="glow-button w-full py-4 rounded-2xl font-black text-lg bg-gradient-to-r from-amber-400 via-orange-400 to-amber-500 text-slate-950 uppercase tracking-wider hover:brightness-110 active:scale-95 transition">
                <i class="fa-solid fa-play mr-2"></i> BẮT ĐẦU CHƠI
            </button>
        </div>
    </div>

    <!-- Game Over Screen -->
    <div id="game-over-screen" class="hidden absolute inset-0 z-30 flex items-center justify-center p-4 bg-slate-950/85 backdrop-blur-md">
        <div class="glass rounded-3xl p-8 max-w-md w-full text-center text-white shadow-2xl border border-rose-500/30">
            <div class="text-rose-500 text-6xl mb-2">
                <i class="fa-solid fa-skull"></i>
            </div>
            <h2 id="game-over-title" class="text-3xl font-black text-rose-400 uppercase tracking-wide">Bị Tiêu Diệt!</h2>
            <p id="game-over-sub" class="text-slate-400 text-sm mt-1 mb-6">Bạn đã va chạm với một chú sâu khác!</p>

            <div class="bg-slate-900/60 rounded-2xl p-4 mb-6 space-y-2 border border-slate-800">
                <div class="flex justify-between items-center">
                    <span class="text-slate-400 text-sm">Điểm số đạt được:</span>
                    <span id="final-score" class="text-2xl font-bold text-amber-400">0</span>
                </div>
                <div class="flex justify-between items-center">
                    <span class="text-slate-400 text-sm">Chiều dài tối đa:</span>
                    <span id="final-length" class="text-lg font-bold text-slate-200">0m</span>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-3">
                <button id="main-menu-btn" class="py-3.5 rounded-xl font-bold text-sm bg-slate-800 border border-slate-600 text-slate-200 uppercase hover:bg-slate-700 transition">
                    <i class="fa-solid fa-house mr-1"></i> Menu Chính
                </button>
                <button id="restart-btn" class="glow-button py-3.5 rounded-xl font-black text-sm bg-gradient-to-r from-amber-400 to-orange-500 text-slate-950 uppercase tracking-wider hover:brightness-110 active:scale-95 transition">
                    <i class="fa-solid fa-rotate-right mr-1"></i> Chơi Lại
                </button>
            </div>
        </div>
    </div>

    <script>
        // --- GAME CONFIGURATION & CONSTANTS ---
        const WORLD_SIZE = 3000;
        const INITIAL_SNAKE_LENGTH = 15;
        const BASE_SPEED = 3.5;
        const BOOST_SPEED = 7.0;
        const SEGMENT_DISTANCE = 8;
        const AI_COUNT = 25;
        const FOOD_COUNT = 800;

        // SKINS DATABASE
        const SKINS = [
            { id: 'royal', name: 'Hoàng Gia', main: '#f59e0b', stripe: '#fef08a' },
            { id: 'watermelon', name: 'Dưa Hấu', main: '#10b981', stripe: '#f43f5e' },
            { id: 'rainbow', name: 'Cầu Cồng', main: '#0284c7', stripe: '#bae6fd' },
            { id: 'shadow', name: 'Đen Ngầu', main: '#334155', stripe: '#94a3b8' },
            { id: 'cute_pink', name: 'Hồng Đáng Yêu', main: '#ec4899', stripe: '#fbcfe8' }
        ];

        let currentSkinIndex = 0;
        let currentGameMode = 'endless'; // 'endless' or 'time'
        let gameTimeLeft = 180; // 3 minutes for Time Attack
        let timerInterval = null;
        let highScore = localStorage.getItem('snake_high_score') || 0;

        // --- AUDIO ENGINE (WEB AUDIO API SYNTHESIZER & BGM) ---
        class WebAudioManager {
            constructor() {
                this.ctx = null;
                this.bgmEnabled = true;
                this.sfxEnabled = true;
                this.bgmVolume = 0.7;
                this.sfxVolume = 0.8;
                this.currentTrack = 'arcade';
                this.isPlayingBGM = false;
                this.bgmTimer = null;
                this.step = 0;
                this.wasTop1 = false;
            }

            init() {
                if (!this.ctx) {
                    this.ctx = new (window.AudioContext || window.webkitAudioContext)();
                }
                if (this.ctx.state === 'suspended') {
                    this.ctx.resume();
                }
            }

            // --- BGM SYNTH TRACKS ---
            startBGM() {
                this.init();
                if (!this.bgmEnabled || this.isPlayingBGM) return;
                this.isPlayingBGM = true;
                this.step = 0;
                this.scheduleNextBGMStep();
            }

            stopBGM() {
                this.isPlayingBGM = false;
                if (this.bgmTimer) {
                    clearTimeout(this.bgmTimer);
                    this.bgmTimer = null;
                }
            }

            scheduleNextBGMStep() {
                if (!this.isPlayingBGM || !this.bgmEnabled) return;

                const tracks = {
                    arcade: { bpm: 125, notes: [261.63, 293.66, 329.63, 349.23, 392.00, 440.00, 493.88, 523.25], type: 'triangle' },
                    edm: { bpm: 135, notes: [130.81, 164.81, 196.00, 220.00, 261.63, 329.63], type: 'sawtooth' },
                    retro: { bpm: 140, notes: [220.00, 277.18, 329.63, 440.00, 554.37, 659.25], type: 'square' },
                    chill: { bpm: 90, notes: [174.61, 220.00, 261.63, 329.63, 392.00], type: 'sine' }
                };

                const track = tracks[this.currentTrack] || tracks.arcade;
                const stepTime = (60 / track.bpm) / 2; // 8th notes

                // Play Note
                this.playBGMSynthNote(track, this.step);

                this.step = (this.step + 1) % 16;
                this.bgmTimer = setTimeout(() => this.scheduleNextBGMStep(), stepTime * 1000);
            }

            playBGMSynthNote(track, step) {
                if (!this.ctx) return;

                const now = this.ctx.currentTime;
                const osc = this.ctx.createOscillator();
                const gain = this.ctx.createGain();

                osc.type = track.type;

                // Simple melody pattern logic
                let noteFreq = track.notes[step % track.notes.length];
                if (step % 4 === 0) noteFreq *= 0.5; // Bassline pulse on beat

                osc.frequency.setValueAtTime(noteFreq, now);

                const vol = this.bgmVolume * (step % 4 === 0 ? 0.15 : 0.08);
                gain.gain.setValueAtTime(vol, now);
                gain.gain.exponentialRampToValueAtTime(0.001, now + 0.15);

                osc.connect(gain);
                gain.connect(this.ctx.destination);

                osc.start(now);
                osc.stop(now + 0.15);
            }

            // --- SFX SOUNDS ---
            playClick() {
                if (!this.sfxEnabled) return;
                this.init();
                const osc = this.ctx.createOscillator();
                const gain = this.ctx.createGain();
                osc.type = 'sine';
                osc.frequency.setValueAtTime(600, this.ctx.currentTime);
                osc.frequency.exponentialRampToValueAtTime(1200, this.ctx.currentTime + 0.05);
                gain.gain.setValueAtTime(0.15 * this.sfxVolume, this.ctx.currentTime);
                gain.gain.linearRampToValueAtTime(0.01, this.ctx.currentTime + 0.05);
                osc.connect(gain);
                gain.connect(this.ctx.destination);
                osc.start();
                osc.stop(this.ctx.currentTime + 0.05);
            }

            playEat() {
                if (!this.sfxEnabled) return;
                this.init();
                const osc = this.ctx.createOscillator();
                const gain = this.ctx.createGain();
                osc.type = 'sine';
                const freq = 400 + Math.random() * 250;
                osc.frequency.setValueAtTime(freq, this.ctx.currentTime);
                osc.frequency.exponentialRampToValueAtTime(freq * 1.8, this.ctx.currentTime + 0.08);
                gain.gain.setValueAtTime(0.12 * this.sfxVolume, this.ctx.currentTime);
                gain.gain.linearRampToValueAtTime(0.01, this.ctx.currentTime + 0.08);
                osc.connect(gain);
                gain.connect(this.ctx.destination);
                osc.start();
                osc.stop(this.ctx.currentTime + 0.08);
            }

            playDie() {
                if (!this.sfxEnabled) return;
                this.init();
                const osc = this.ctx.createOscillator();
                const gain = this.ctx.createGain();
                osc.type = 'sawtooth';
                osc.frequency.setValueAtTime(280, this.ctx.currentTime);
                osc.frequency.exponentialRampToValueAtTime(40, this.ctx.currentTime + 0.35);
                gain.gain.setValueAtTime(0.3 * this.sfxVolume, this.ctx.currentTime);
                gain.gain.linearRampToValueAtTime(0.01, this.ctx.currentTime + 0.35);
                osc.connect(gain);
                gain.connect(this.ctx.destination);
                osc.start();
                osc.stop(this.ctx.currentTime + 0.35);
            }

            playCrownVictory() {
                if (!this.sfxEnabled) return;
                this.init();
                const now = this.ctx.currentTime;
                [523.25, 659.25, 783.99, 1046.50].forEach((freq, idx) => {
                    const osc = this.ctx.createOscillator();
                    const gain = this.ctx.createGain();
                    osc.type = 'triangle';
                    osc.frequency.setValueAtTime(freq, now + idx * 0.08);
                    gain.gain.setValueAtTime(0.2 * this.sfxVolume, now + idx * 0.08);
                    gain.gain.exponentialRampToValueAtTime(0.001, now + idx * 0.08 + 0.25);
                    osc.connect(gain);
                    gain.connect(this.ctx.destination);
                    osc.start(now + idx * 0.08);
                    osc.stop(now + idx * 0.08 + 0.25);
                });
            }
        }

        const audioMgr = new WebAudioManager();

        // --- SETUP CANVAS & STATE ---
        const canvas = document.getElementById('game-canvas');
        const ctx = canvas.getContext('2d');
        const minimapCanvas = document.getElementById('minimap');
        const minimapCtx = minimapCanvas.getContext('2d');

        let width = canvas.width = window.innerWidth;
        let height = canvas.height = window.innerHeight;

        window.addEventListener('resize', () => {
            width = canvas.width = window.innerWidth;
            height = canvas.height = window.innerHeight;
        });

        // Mouse & Touch Position
        let mouseX = width / 2;
        let mouseY = height / 2;
        let isBoosting = false;

        // Customization
        let selectedColor = SKINS[0].main;
        let selectedStripe = SKINS[0].stripe;

        // Camera Offset
        let camera = { x: 0, y: 0 };

        // Game Entities
        let player = null;
        let snakes = [];
        let foods = [];
        let isGameRunning = false;

        // Render Skins in Menu
        function renderSkinSelector() {
            const grid = document.getElementById('skin-grid');
            grid.innerHTML = '';
            SKINS.forEach((skin, index) => {
                const btn = document.createElement('button');
                btn.className = `skin-card p-2 bg-slate-800/80 rounded-xl border-2 border-slate-700 flex flex-col items-center gap-1 cursor-pointer hover:border-slate-500 ${index === currentSkinIndex ? 'selected' : ''}`;
                btn.innerHTML = `
                    <div class="w-8 h-8 rounded-full flex items-center justify-center border border-white/20" style="background: radial-gradient(circle, ${skin.stripe} 30%, ${skin.main} 100%);"></div>
                    <span class="text-[10px] font-bold text-slate-300 truncate w-full text-center">${skin.name}</span>
                `;
                btn.addEventListener('click', () => {
                    audioMgr.playClick();
                    currentSkinIndex = index;
                    selectedColor = skin.main;
                    selectedStripe = skin.stripe;
                    renderSkinSelector();
                });
                grid.appendChild(btn);
            });
        }

        // --- FOOD CLASS ---
        class Food {
            constructor(x, y, radius, value, color = null) {
                this.x = x || Math.random() * WORLD_SIZE;
                this.y = y || Math.random() * WORLD_SIZE;
                this.radius = radius || (Math.random() * 3 + 3);
                this.value = value || Math.floor(this.radius);
                this.color = color || `hsl(${Math.random() * 360}, 80%, 60%)`;
                this.pulse = Math.random() * Math.PI * 2;
            }

            draw(ctx, camera) {
                const screenX = this.x - camera.x;
                const screenY = this.y - camera.y;

                if (screenX < -20 || screenX > width + 20 || screenY < -20 || screenY > height + 20) return;

                this.pulse += 0.05;
                const currentRadius = this.radius + Math.sin(this.pulse) * 0.8;

                ctx.save();
                ctx.beginPath();
                ctx.arc(screenX, screenY, Math.max(1, currentRadius), 0, Math.PI * 2);
                ctx.fillStyle = this.color;
                ctx.shadowColor = this.color;
                ctx.shadowBlur = 8;
                ctx.fill();
                ctx.restore();
            }
        }

        // --- SNAKE CLASS ---
        class Snake {
            constructor(id, name, x, y, isAI = false, mainColor = '#f59e0b', stripeColor = '#fef08a') {
                this.id = id;
                this.name = name;
                this.isAI = isAI;
                this.mainColor = mainColor;
                this.stripeColor = stripeColor;
                this.x = x;
                this.y = y;
                this.angle = Math.random() * Math.PI * 2;
                this.targetAngle = this.angle;
                this.speed = BASE_SPEED;
                this.radius = 16;
                this.score = 0;
                this.isDead = false;
                this.isBoosting = false;

                this.segments = [];
                const initialLength = INITIAL_SNAKE_LENGTH + Math.floor(Math.random() * 10);
                for (let i = 0; i < initialLength; i++) {
                    this.segments.push({
                        x: this.x - Math.cos(this.angle) * i * SEGMENT_DISTANCE,
                        y: this.y - Math.sin(this.angle) * i * SEGMENT_DISTANCE
                    });
                }

                this.aiTimer = 0;
                this.targetFood = null;
            }

            update() {
                if (this.isDead) return;

                this.radius = 16 + Math.min(Math.floor(this.score / 200), 20);
                this.speed = this.isBoosting ? BOOST_SPEED : BASE_SPEED;

                if (this.isAI) {
                    this.updateAI();
                } else {
                    this.isBoosting = isBoosting;
                    const dx = mouseX - width / 2;
                    const dy = mouseY - height / 2;
                    if (Math.hypot(dx, dy) > 20) {
                        this.targetAngle = Math.atan2(dy, dx);
                    }
                }

                let diff = this.targetAngle - this.angle;
                while (diff < -Math.PI) diff += Math.PI * 2;
                while (diff > Math.PI) diff -= Math.PI * 2;
                this.angle += diff * 0.12;

                this.x += Math.cos(this.angle) * this.speed;
                this.y += Math.sin(this.angle) * this.speed;

                this.x = Math.max(this.radius, Math.min(WORLD_SIZE - this.radius, this.x));
                this.y = Math.max(this.radius, Math.min(WORLD_SIZE - this.radius, this.y));

                let head = { x: this.x, y: this.y };
                this.segments[0] = head;

                for (let i = 1; i < this.segments.length; i++) {
                    const prev = this.segments[i - 1];
                    const curr = this.segments[i];
                    const dx = prev.x - curr.x;
                    const dy = prev.y - curr.y;
                    const dist = Math.hypot(dx, dy);

                    if (dist > SEGMENT_DISTANCE) {
                        const angle = Math.atan2(dy, dx);
                        curr.x = prev.x - Math.cos(angle) * SEGMENT_DISTANCE;
                        curr.y = prev.y - Math.sin(angle) * SEGMENT_DISTANCE;
                    }
                }

                // Boost particle drop
                if (this.isBoosting && this.segments.length > 10 && Math.random() < 0.3) {
                    const tail = this.segments[this.segments.length - 1];
                    foods.push(new Food(tail.x + (Math.random()-0.5)*10, tail.y + (Math.random()-0.5)*10, 3, 1, this.mainColor));
                    if (Math.random() < 0.1) {
                        this.segments.pop();
                    }
                }
            }

            updateAI() {
                this.aiTimer++;

                if (this.aiTimer % 15 === 0 || !this.targetFood) {
                    let closestDist = 300;
                    this.targetFood = null;

                    for (let f of foods) {
                        const d = Math.hypot(f.x - this.x, f.y - this.y);
                        if (d < closestDist) {
                            closestDist = d;
                            this.targetFood = f;
                        }
                    }
                }

                if (this.targetFood) {
                    this.targetAngle = Math.atan2(this.targetFood.y - this.y, this.targetFood.x - this.x);
                } else if (this.aiTimer % 60 === 0) {
                    this.targetAngle += (Math.random() - 0.5) * 1.5;
                }

                const margin = 100;
                if (this.x < margin) this.targetAngle = 0;
                if (this.x > WORLD_SIZE - margin) this.targetAngle = Math.PI;
                if (this.y < margin) this.targetAngle = Math.PI / 2;
                if (this.y > WORLD_SIZE - margin) this.targetAngle = -Math.PI / 2;

                if (this.segments.length > 25 && Math.random() < 0.01) {
                    this.isBoosting = !this.isBoosting;
                }
            }

            draw(ctx, camera, isTopLeader = false) {
                if (this.isDead) return;

                for (let i = this.segments.length - 1; i >= 0; i--) {
                    const seg = this.segments[i];
                    const screenX = seg.x - camera.x;
                    const screenY = seg.y - camera.y;

                    if (screenX < -50 || screenX > width + 50 || screenY < -50 || screenY > height + 50) continue;

                    ctx.save();
                    ctx.translate(screenX, screenY);

                    if (i === 0) {
                        ctx.rotate(this.angle);

                        if (this.isBoosting) {
                            ctx.shadowColor = '#fbbf24';
                            ctx.shadowBlur = 15;
                        }

                        ctx.beginPath();
                        ctx.arc(0, 0, this.radius, 0, Math.PI * 2);
                        ctx.fillStyle = this.mainColor;
                        ctx.fill();
                        ctx.lineWidth = 2.5;
                        ctx.strokeStyle = '#ffffff';
                        ctx.stroke();

                        const eyeOffset = this.radius * 0.45;
                        const eyeRadius = this.radius * 0.38;

                        // Eyes
                        ctx.beginPath();
                        ctx.arc(eyeOffset, -eyeOffset, eyeRadius, 0, Math.PI * 2);
                        ctx.fillStyle = '#ffffff';
                        ctx.fill();
                        ctx.lineWidth = 1;
                        ctx.strokeStyle = '#000';
                        ctx.stroke();

                        ctx.beginPath();
                        ctx.arc(eyeOffset, eyeOffset, eyeRadius, 0, Math.PI * 2);
                        ctx.fillStyle = '#ffffff';
                        ctx.fill();
                        ctx.stroke();

                        // Pupils
                        ctx.beginPath();
                        ctx.arc(eyeOffset + eyeRadius*0.2, -eyeOffset, eyeRadius*0.55, 0, Math.PI * 2);
                        ctx.arc(eyeOffset + eyeRadius*0.2, eyeOffset, eyeRadius*0.55, 0, Math.PI * 2);
                        ctx.fillStyle = '#0f172a';
                        ctx.fill();

                        // Eye Highlights
                        ctx.beginPath();
                        ctx.arc(eyeOffset + eyeRadius*0.35, -eyeOffset - eyeRadius*0.15, eyeRadius*0.2, 0, Math.PI * 2);
                        ctx.arc(eyeOffset + eyeRadius*0.35, eyeOffset - eyeRadius*0.15, eyeRadius*0.2, 0, Math.PI * 2);
                        ctx.fillStyle = '#ffffff';
                        ctx.fill();

                        // Mouth
                        ctx.beginPath();
                        ctx.arc(this.radius * 0.3, 0, this.radius * 0.3, 0.1 * Math.PI, 0.9 * Math.PI, false);
                        ctx.lineWidth = 2;
                        ctx.strokeStyle = '#000000';
                        ctx.stroke();

                        // Crown Icon for Top Leader
                        if (isTopLeader) {
                            ctx.rotate(-this.angle);
                            ctx.fillStyle = '#fbbf24';
                            ctx.font = '900 22px "Font Awesome 6 Free"';
                            ctx.textAlign = 'center';
                            ctx.fillText('\uf521', 0, -this.radius - 8);
                        }

                    } else {
                        const scale = 1 - (i / this.segments.length) * 0.25;
                        const currentRadius = this.radius * scale;

                        ctx.beginPath();
                        ctx.arc(0, 0, currentRadius, 0, Math.PI * 2);
                        ctx.fillStyle = (i % 3 === 0) ? this.stripeColor : this.mainColor;
                        ctx.fill();
                        ctx.lineWidth = 1.5;
                        ctx.strokeStyle = 'rgba(0,0,0,0.15)';
                        ctx.stroke();
                    }

                    ctx.restore();
                }

                const headScreenX = this.x - camera.x;
                const headScreenY = this.y - camera.y;
                if (headScreenX >= 0 && headScreenX <= width && headScreenY >= 0 && headScreenY <= height) {
                    ctx.save();
                    ctx.font = 'bold 12px sans-serif';
                    ctx.fillStyle = '#ffffff';
                    ctx.textAlign = 'center';
                    ctx.shadowColor = '#000000';
                    ctx.shadowBlur = 4;
                    ctx.fillText(this.name, headScreenX, headScreenY - this.radius - 12);
                    ctx.restore();
                }
            }

            grow(amount) {
                this.score += amount;
                const addCount = Math.floor(amount / 2);
                for (let i = 0; i < addCount; i++) {
                    const tail = this.segments[this.segments.length - 1];
                    this.segments.push({ x: tail.x, y: tail.y });
                }
            }

            die() {
                if (this.isDead) return;
                this.isDead = true;

                for (let i = 0; i < this.segments.length; i += 2) {
                    const seg = this.segments[i];
                    foods.push(new Food(
                        seg.x + (Math.random() - 0.5) * 15,
                        seg.y + (Math.random() - 0.5) * 15,
                        Math.random() * 4 + 4,
                        3,
                        this.mainColor
                    ));
                }
            }
        }

        // --- GAME CONTROLLER & LOGIC ---

        function initGame() {
            audioMgr.playClick();
            audioMgr.startBGM();

            const playerName = document.getElementById('player-name').value.trim() || 'Sâu Béo';
            
            player = new Snake('player', playerName, WORLD_SIZE / 2, WORLD_SIZE / 2, false, selectedColor, selectedStripe);

            snakes = [player];
            const aiNames = ['SlitherPro', 'Viper', 'Speedy', 'Cobra', 'GlowWorm', 'Shadow', 'Noodle', 'Slinky', 'Apex', 'Titan', 'Mamba', 'ZigZag', 'Dragon'];

            for (let i = 0; i < AI_COUNT; i++) {
                const name = aiNames[i % aiNames.length] + '_' + Math.floor(Math.random() * 99);
                const colors = [
                    { main: '#10b981', stripe: '#a7f3d0' },
                    { main: '#0284c7', stripe: '#bae6fd' },
                    { main: '#a855f7', stripe: '#f0abfc' },
                    { main: '#f43f5e', stripe: '#fecdd3' },
                    { main: '#eab308', stripe: '#fef08a' }
                ];
                const c = colors[Math.floor(Math.random() * colors.length)];
                const x = Math.random() * (WORLD_SIZE - 200) + 100;
                const y = Math.random() * (WORLD_SIZE - 200) + 100;
                snakes.push(new Snake(`ai_${i}`, name, x, y, true, c.main, c.stripe));
            }

            foods = [];
            for (let i = 0; i < FOOD_COUNT; i++) {
                foods.push(new Food());
            }

            if (timerInterval) clearInterval(timerInterval);
            const timerBox = document.getElementById('timer-box');
            if (currentGameMode === 'time') {
                gameTimeLeft = 180;
                timerBox.classList.remove('hidden');
                updateTimerDisplay();
                timerInterval = setInterval(() => {
                    gameTimeLeft--;
                    updateTimerDisplay();
                    if (gameTimeLeft <= 0) {
                        clearInterval(timerInterval);
                        timeUpGameOver();
                    }
                }, 1000);
            } else {
                timerBox.classList.add('hidden');
            }

            isGameRunning = true;
            document.getElementById('start-screen').classList.add('hidden');
            document.getElementById('game-over-screen').classList.add('hidden');
            document.getElementById('hud').classList.remove('hidden');
        }

        function updateTimerDisplay() {
            const mins = Math.floor(gameTimeLeft / 60);
            const secs = gameTimeLeft % 60;
            document.getElementById('timer-val').innerText = `${mins.toString().padStart(2, '0')}:${secs.toString().padStart(2, '0')}`;
        }

        function timeUpGameOver() {
            gameOver("HẾT GIỜ!", "Thời gian 3 phút đã kết thúc!");
        }

        function checkCollisions() {
            for (let snake of snakes) {
                if (snake.isDead) continue;

                for (let i = foods.length - 1; i >= 0; i--) {
                    const f = foods[i];
                    const dist = Math.hypot(snake.x - f.x, snake.y - f.y);
                    if (dist < snake.radius + f.radius + 2) {
                        snake.grow(f.value);
                        if (snake === player) audioMgr.playEat();
                        foods.splice(i, 1);
                        foods.push(new Food());
                    }
                }
            }

            for (let s1 of snakes) {
                if (s1.isDead) continue;

                for (let s2 of snakes) {
                    if (s2.isDead) continue;

                    for (let i = 0; i < s2.segments.length; i++) {
                        if (s1 === s2 && i < 4) continue;

                        const seg = s2.segments[i];
                        const dist = Math.hypot(s1.x - seg.x, s1.y - seg.y);

                        if (dist < s1.radius * 0.85 + (s2.radius * 0.7)) {
                            s1.die();
                            if (s1 === player) {
                                audioMgr.playDie();
                                gameOver("BỊ TIÊU DIỆT!", "Bạn đã va chạm với một chú sâu khác!");
                            } else {
                                setTimeout(() => {
                                    if (isGameRunning) {
                                        const index = snakes.indexOf(s1);
                                        if (index !== -1) {
                                            const x = Math.random() * (WORLD_SIZE - 200) + 100;
                                            const y = Math.random() * (WORLD_SIZE - 200) + 100;
                                            snakes[index] = new Snake(s1.id, s1.name, x, y, true, s1.mainColor, s1.stripeColor);
                                        }
                                    }
                                }, 3000);
                            }
                            break;
                        }
                    }
                }
            }
        }

        function drawBackgroundGrid() {
            ctx.save();
            ctx.strokeStyle = '#1e293b';
            ctx.lineWidth = 1;

            const gridSize = 80;
            const startX = Math.floor(camera.x / gridSize) * gridSize - camera.x;
            const startY = Math.floor(camera.y / gridSize) * gridSize - camera.y;

            for (let x = startX; x < width; x += gridSize) {
                ctx.beginPath();
                ctx.moveTo(x, 0);
                ctx.lineTo(x, height);
                ctx.stroke();
            }

            for (let y = startY; y < height; y += gridSize) {
                ctx.beginPath();
                ctx.moveTo(0, y);
                ctx.lineTo(width, y);
                ctx.stroke();
            }

            ctx.strokeStyle = '#ef4444';
            ctx.lineWidth = 8;
            ctx.strokeRect(-camera.x, -camera.y, WORLD_SIZE, WORLD_SIZE);

            ctx.restore();
        }

        function updateHUD(topSnake) {
            if (!player) return;

            document.getElementById('score-val').innerText = player.score;
            document.getElementById('length-val').innerText = player.segments.length;

            const sortedSnakes = [...snakes].filter(s => !s.isDead).sort((a, b) => b.score - a.score).slice(0, 5);
            const leaderboardEl = document.getElementById('leaderboard-list');
            leaderboardEl.innerHTML = '';

            sortedSnakes.forEach((s, idx) => {
                const li = document.createElement('li');
                li.className = `flex justify-between items-center ${s === player ? 'text-amber-400 font-bold' : 'text-slate-300'}`;
                li.innerHTML = `
                    <span class="truncate max-w-[120px]">${idx + 1}. ${s.name}</span>
                    <span class="font-mono text-xs">${s.score}</span>
                `;
                leaderboardEl.appendChild(li);
            });

            // Crown Victory Sound Trigger
            if (topSnake === player && player.score > 0 && !audioMgr.wasTop1) {
                audioMgr.wasTop1 = true;
                audioMgr.playCrownVictory();
            } else if (topSnake !== player) {
                audioMgr.wasTop1 = false;
            }

            // Minimap Draw
            minimapCtx.clearRect(0, 0, 120, 120);
            const scale = 120 / WORLD_SIZE;

            minimapCtx.fillStyle = '#f59e0b';
            minimapCtx.beginPath();
            minimapCtx.arc(player.x * scale, player.y * scale, 3, 0, Math.PI * 2);
            minimapCtx.fill();

            minimapCtx.fillStyle = 'rgba(255, 255, 255, 0.4)';
            snakes.forEach(s => {
                if (s !== player && !s.isDead) {
                    minimapCtx.fillRect(s.x * scale, s.y * scale, 2, 2);
                }
            });
        }

        function gameOver(title = "BỊ TIÊU DIỆT!", subtitle = "Bạn đã va chạm với một chú sâu khác!") {
            isGameRunning = false;
            if (timerInterval) clearInterval(timerInterval);

            if (player && player.score > highScore) {
                highScore = player.score;
                localStorage.setItem('snake_high_score', highScore);
                document.getElementById('high-score-display').innerText = highScore;
            }

            document.getElementById('game-over-title').innerText = title;
            document.getElementById('game-over-sub').innerText = subtitle;
            document.getElementById('final-score').innerText = player ? player.score : 0;
            document.getElementById('final-length').innerText = player ? (player.segments.length + 'm') : '0m';
            document.getElementById('game-over-screen').classList.remove('hidden');
        }

        function showMainMenu() {
            audioMgr.playClick();
            isGameRunning = false;
            if (timerInterval) clearInterval(timerInterval);
            document.getElementById('hud').classList.add('hidden');
            document.getElementById('game-over-screen').classList.add('hidden');
            document.getElementById('start-screen').classList.remove('hidden');
            document.getElementById('high-score-display').innerText = highScore;
        }

        // --- MAIN GAME LOOP ---
        function gameLoop() {
            ctx.clearRect(0, 0, width, height);

            if (isGameRunning && player) {
                camera.x += (player.x - width / 2 - camera.x) * 0.1;
                camera.y += (player.y - height / 2 - camera.y) * 0.1;

                snakes.forEach(s => s.update());
                checkCollisions();

                drawBackgroundGrid();

                foods.forEach(f => f.draw(ctx, camera));

                const sorted = [...snakes].filter(s => !s.isDead).sort((a, b) => b.score - a.score);
                const topSnake = sorted.length > 0 ? sorted[0] : null;

                snakes.forEach(s => s.draw(ctx, camera, s === topSnake && s.score > 0));

                updateHUD(topSnake);
            }

            requestAnimationFrame(gameLoop);
        }

        // --- EVENT LISTENERS ---
        window.addEventListener('mousemove', (e) => {
            mouseX = e.clientX;
            mouseY = e.clientY;
        });

        window.addEventListener('mousedown', (e) => {
            if (e.button === 0) isBoosting = true;
        });

        window.addEventListener('mouseup', () => {
            isBoosting = false;
        });

        window.addEventListener('touchmove', (e) => {
            if (e.touches.length > 0) {
                mouseX = e.touches[0].clientX;
                mouseY = e.touches[0].clientY;
            }
        });

        window.addEventListener('touchstart', (e) => {
            if (e.touches.length > 0) {
                mouseX = e.touches[0].clientX;
                mouseY = e.touches[0].clientY;
            }
        });

        window.addEventListener('keydown', (e) => {
            if (e.code === 'Space') isBoosting = true;
        });

        window.addEventListener('keyup', (e) => {
            if (e.code === 'Space') isBoosting = false;
        });

        // Mode Selectors
        document.getElementById('mode-endless').addEventListener('click', () => {
            audioMgr.playClick();
            currentGameMode = 'endless';
            document.getElementById('mode-endless').className = 'mode-btn selected bg-slate-800/90 border-2 border-amber-400 rounded-xl p-3 text-left transition flex items-center gap-3';
            document.getElementById('mode-time').className = 'mode-btn bg-slate-800/90 border-2 border-slate-700 rounded-xl p-3 text-left transition flex items-center gap-3 opacity-70 hover:opacity-100';
        });

        document.getElementById('mode-time').addEventListener('click', () => {
            audioMgr.playClick();
            currentGameMode = 'time';
            document.getElementById('mode-time').className = 'mode-btn selected bg-slate-800/90 border-2 border-rose-400 rounded-xl p-3 text-left transition flex items-center gap-3';
            document.getElementById('mode-endless').className = 'mode-btn bg-slate-800/90 border-2 border-slate-700 rounded-xl p-3 text-left transition flex items-center gap-3 opacity-70 hover:opacity-100';
        });

        // AUDIO CONTROLS HANDLERS
        document.getElementById('bgm-toggle-btn').addEventListener('click', () => {
            audioMgr.bgmEnabled = !audioMgr.bgmEnabled;
            const btn = document.getElementById('bgm-toggle-btn');
            if (audioMgr.bgmEnabled) {
                btn.innerHTML = '<i class="fa-solid fa-music mr-1"></i> BGM: ON';
                btn.className = 'px-2.5 py-1 text-xs rounded-lg bg-amber-500/20 text-amber-300 border border-amber-500/30 font-bold hover:bg-amber-500/30 transition';
                audioMgr.startBGM();
            } else {
                btn.innerHTML = '<i class="fa-solid fa-music-slash mr-1"></i> BGM: OFF';
                btn.className = 'px-2.5 py-1 text-xs rounded-lg bg-slate-700/50 text-slate-400 border border-slate-600 font-bold hover:bg-slate-700 transition';
                audioMgr.stopBGM();
            }
        });

        document.getElementById('sfx-toggle-btn').addEventListener('click', () => {
            audioMgr.sfxEnabled = !audioMgr.sfxEnabled;
            const btn = document.getElementById('sfx-toggle-btn');
            if (audioMgr.sfxEnabled) {
                btn.innerHTML = '<i class="fa-solid fa-volume-high mr-1"></i> SFX: ON';
                btn.className = 'px-2.5 py-1 text-xs rounded-lg bg-amber-500/20 text-amber-300 border border-amber-500/30 font-bold hover:bg-amber-500/30 transition';
            } else {
                btn.innerHTML = '<i class="fa-solid fa-volume-xmark mr-1"></i> SFX: OFF';
                btn.className = 'px-2.5 py-1 text-xs rounded-lg bg-slate-700/50 text-slate-400 border border-slate-600 font-bold hover:bg-slate-700 transition';
            }
        });

        document.getElementById('bgm-track-select').addEventListener('change', (e) => {
            audioMgr.currentTrack = e.target.value;
            audioMgr.playClick();
        });

        document.getElementById('bgm-vol-slider').addEventListener('input', (e) => {
            audioMgr.bgmVolume = parseFloat(e.target.value);
            document.getElementById('bgm-vol-val').innerText = Math.round(audioMgr.bgmVolume * 100) + '%';
        });

        document.getElementById('hud-sound-btn').addEventListener('click', () => {
            audioMgr.sfxEnabled = !audioMgr.sfxEnabled;
            audioMgr.bgmEnabled = audioMgr.sfxEnabled;
            const icon = document.getElementById('hud-sound-icon');
            if (audioMgr.sfxEnabled) {
                icon.className = 'fa-solid fa-volume-high text-amber-400';
                audioMgr.startBGM();
            } else {
                icon.className = 'fa-solid fa-volume-xmark text-slate-500';
                audioMgr.stopBGM();
            }
        });

        document.getElementById('start-btn').addEventListener('click', initGame);
        document.getElementById('restart-btn').addEventListener('click', initGame);
        document.getElementById('main-menu-btn').addEventListener('click', showMainMenu);

        window.onload = () => {
            renderSkinSelector();
            document.getElementById('high-score-display').innerText = highScore;
            gameLoop();
        };
    </script>
</body>
</html>
