# Temple-run-
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Temple Escape - 3D Endless Runner</title>
    <!-- Tailwind CSS for UI styling -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Three.js CDN -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <!-- FontAwesome for UI Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Cinzel:wght@700;900&family=Outfit:wght@400;600;800&display=swap');
        
        body, html {
            margin: 0;
            padding: 0;
            width: 100%;
            height: 100%;
            overflow: hidden;
            font-family: 'Outfit', sans-serif;
            user-select: none;
            -webkit-user-select: none;
            background-color: #050508;
        }

        .font-cinzel {
            font-family: 'Cinzel', serif;
        }

        #game-canvas {
            width: 100%;
            height: 100%;
            display: block;
        }

        /* Custom Gold Glow & Glassmorphism UI Effects */
        .gold-text-glow {
            text-shadow: 0 0 10px rgba(234, 179, 8, 0.6), 0 0 20px rgba(234, 179, 8, 0.4);
        }

        .gold-border-glow {
            box-shadow: 0 0 15px rgba(234, 179, 8, 0.4), inset 0 0 15px rgba(234, 179, 8, 0.2);
        }

        .temple-panel {
            background: linear-gradient(135deg, rgba(20, 15, 10, 0.92) 0%, rgba(5, 5, 8, 0.96) 100%);
            border: 2px solid #ca8a04;
        }

        .btn-gold {
            background: linear-gradient(180deg, #facc15 0%, #ca8a04 100%);
            color: #1a0f00;
            box-shadow: 0 4px 15px rgba(202, 138, 4, 0.4);
            transition: all 0.2s ease;
        }

        .btn-gold:hover {
            transform: translateY(-2px);
            box-shadow: 0 6px 20px rgba(250, 204, 21, 0.6);
            background: linear-gradient(180deg, #fde047 0%, #d97706 100%);
        }

        .btn-gold:active {
            transform: translateY(1px);
        }

        /* Mobile Touch Overlay Vignette */
        .vignette {
            box-shadow: inset 0 0 100px rgba(0,0,0,0.8);
        }
    </style>
</head>
<body class="bg-black text-white">

    <!-- Game 3D Canvas -->
    <canvas id="game-canvas"></canvas>

    <!-- Overlay Vignette & Touch Interface -->
    <div class="vignette fixed inset-0 pointer-events-none z-10"></div>

    <!-- HUD (Heads-Up Display) -->
    <div id="hud" class="fixed top-0 left-0 w-full p-4 sm:p-6 flex justify-between items-start z-20 pointer-events-none hidden">
        <!-- Score & Multiplier -->
        <div class="flex flex-col gap-1">
            <div class="bg-black/60 backdrop-blur-md px-4 py-2 rounded-xl border border-yellow-600/40 flex items-center gap-3">
                <i class="fa-solid fa-trophy text-yellow-400 text-xl"></i>
                <div>
                    <div class="text-xs uppercase tracking-wider text-yellow-200/70 font-semibold">Score</div>
                    <div id="hud-score" class="text-2xl font-black text-white font-cinzel tracking-wider">0</div>
                </div>
            </div>
            <div id="multiplier-badge" class="bg-yellow-500/20 text-yellow-300 text-xs font-bold px-3 py-1 rounded-lg border border-yellow-500/40 w-max opacity-0 transition-opacity">
                2x SPEED MULTIPLIER
            </div>
        </div>

        <!-- Coins Count & Pause Button -->
        <div class="flex items-center gap-3">
            <div class="bg-black/60 backdrop-blur-md px-4 py-2 rounded-xl border border-yellow-600/40 flex items-center gap-3">
                <i class="fa-solid fa-coins text-yellow-400 text-xl animate-pulse"></i>
                <div>
                    <div class="text-xs uppercase tracking-wider text-yellow-200/70 font-semibold">Coins</div>
                    <div id="hud-coins" class="text-2xl font-black text-yellow-400 font-cinzel">0</div>
                </div>
            </div>

            <button id="btn-pause" class="pointer-events-auto bg-black/60 hover:bg-yellow-900/40 backdrop-blur-md w-12 h-12 rounded-xl border border-yellow-600/40 flex items-center justify-center text-yellow-400 hover:text-white transition">
                <i class="fa-solid fa-pause text-lg"></i>
            </button>
        </div>
    </div>

    <!-- START SCREEN -->
    <div id="screen-start" class="fixed inset-0 z-30 flex items-center justify-center p-4 bg-black/70 backdrop-blur-sm">
        <div class="temple-panel gold-border-glow max-w-md w-full rounded-2xl p-8 text-center flex flex-col items-center">
            <div class="w-16 h-16 bg-yellow-500/10 rounded-full flex items-center justify-center border border-yellow-500/30 mb-4">
                <i class="fa-solid fa-dungeon text-3xl text-yellow-400"></i>
            </div>
            <h1 class="text-4xl font-black font-cinzel text-yellow-400 tracking-wider gold-text-glow mb-2">TEMPLE ESCAPE</h1>
            <p class="text-yellow-100/70 text-sm mb-6">Outrun the ancient curse! Avoid deadly obstacles and collect golden relics.</p>

            <div class="bg-black/40 border border-yellow-900/50 rounded-xl p-4 w-full mb-6 text-left text-xs text-yellow-200/80 space-y-2">
                <div class="font-bold text-yellow-400 uppercase tracking-wide mb-1 border-b border-yellow-900/40 pb-1">Controls</div>
                <div class="flex justify-between items-center">
                    <span>Move Left / Right</span>
                    <span class="font-mono bg-yellow-900/40 px-2 py-0.5 rounded text-white">A / D or ← / →</span>
                </div>
                <div class="flex justify-between items-center">
                    <span>Jump over Hurdles</span>
                    <span class="font-mono bg-yellow-900/40 px-2 py-0.5 rounded text-white">W / Space / ↑</span>
                </div>
                <div class="flex justify-between items-center">
                    <span>Slide under Traps</span>
                    <span class="font-mono bg-yellow-900/40 px-2 py-0.5 rounded text-white">S / ↓</span>
                </div>
                <div class="text-center pt-2 text-[10px] text-yellow-400/60 font-semibold">Mobile: Swipe Left, Right, Up, Down</div>
            </div>

            <button id="btn-start" class="btn-gold w-full py-4 rounded-xl text-lg font-black font-cinzel tracking-wider flex items-center justify-center gap-2">
                <i class="fa-solid fa-play"></i> START RUN
            </button>
            
            <div class="mt-4 text-xs text-yellow-500/80 flex items-center gap-2">
                <i class="fa-solid fa-crown text-yellow-400"></i> Best Score: <span id="start-high-score" class="font-bold text-white">0</span>
            </div>
        </div>
    </div>

    <!-- PAUSE MENU -->
    <div id="screen-pause" class="fixed inset-0 z-30 flex items-center justify-center p-4 bg-black/80 backdrop-blur-md hidden">
        <div class="temple-panel gold-border-glow max-w-sm w-full rounded-2xl p-6 text-center flex flex-col items-center">
            <h2 class="text-2xl font-black font-cinzel text-yellow-400 mb-6 tracking-wide">GAME PAUSED</h2>
            <div class="w-full space-y-3">
                <button id="btn-resume" class="btn-gold w-full py-3 rounded-xl font-bold tracking-wide">RESUME</button>
                <button id="btn-restart-pause" class="bg-yellow-900/30 hover:bg-yellow-900/50 border border-yellow-600/40 text-yellow-300 w-full py-3 rounded-xl font-bold tracking-wide transition">RESTART</button>
            </div>
        </div>
    </div>

    <!-- GAME OVER SCREEN -->
    <div id="screen-gameover" class="fixed inset-0 z-30 flex items-center justify-center p-4 bg-black/85 backdrop-blur-md hidden">
        <div class="temple-panel gold-border-glow max-w-md w-full rounded-2xl p-8 text-center flex flex-col items-center animate-fade-in">
            <div class="w-16 h-16 bg-red-500/10 rounded-full flex items-center justify-center border border-red-500/30 mb-3 text-red-500">
                <i class="fa-solid fa-skull-crossbones text-3xl"></i>
            </div>
            <h2 class="text-3xl font-black font-cinzel text-red-500 mb-1 tracking-wider">YOU CRASHED!</h2>
            <p class="text-yellow-100/60 text-xs mb-6">The temple claimed another explorer...</p>

            <div class="grid grid-cols-2 gap-4 w-full mb-6">
                <div class="bg-black/50 border border-yellow-900/40 rounded-xl p-3 text-center">
                    <div class="text-[10px] uppercase tracking-wider text-yellow-300/60 font-semibold">Final Score</div>
                    <div id="final-score" class="text-2xl font-black text-white font-cinzel">0</div>
                </div>
                <div class="bg-black/50 border border-yellow-900/40 rounded-xl p-3 text-center">
                    <div class="text-[10px] uppercase tracking-wider text-yellow-300/60 font-semibold">Coins Picked</div>
                    <div id="final-coins" class="text-2xl font-black text-yellow-400 font-cinzel">0</div>
                </div>
            </div>

            <div id="new-high-score-msg" class="hidden text-xs font-bold text-yellow-400 bg-yellow-500/20 border border-yellow-500/40 rounded-lg py-2 px-4 mb-6 animate-bounce">
                🎉 NEW HIGH SCORE! 🎉
            </div>

            <button id="btn-restart" class="btn-gold w-full py-4 rounded-xl text-lg font-black font-cinzel tracking-wider flex items-center justify-center gap-2">
                <i class="fa-solid fa-rotate-right"></i> RUN AGAIN
            </button>
        </div>
    </div>

    <!-- MAIN GAME SCRIPT -->
    <script>
        // ==========================================
        // 1. SOUND EFFECTS ENGINE (Web Audio API)
        // ==========================================
        class SoundEngine {
            constructor() {
                this.ctx = null;
            }

            init() {
                if (!this.ctx) {
                    const AudioContext = window.AudioContext || window.webkitAudioContext;
                    this.ctx = new AudioContext();
                }
                if (this.ctx.state === 'suspended') {
                    this.ctx.resume();
                }
            }

            playCoin() {
                if (!this.ctx) return;
                const now = this.ctx.currentTime;
                const osc = this.ctx.createOscillator();
                const gain = this.ctx.createGain();

                osc.type = 'sine';
                osc.frequency.setValueAtTime(987.77, now); // B5
                osc.frequency.exponentialRampToValueAtTime(1318.51, now + 0.12); // E6

                gain.gain.setValueAtTime(0.25, now);
                gain.gain.exponentialRampToValueAtTime(0.01, now + 0.12);

                osc.connect(gain);
                gain.connect(this.ctx.destination);

                osc.start(now);
                osc.stop(now + 0.12);
            }

            playJump() {
                if (!this.ctx) return;
                const now = this.ctx.currentTime;
                const osc = this.ctx.createOscillator();
                const gain = this.ctx.createGain();

                osc.type = 'triangle';
                osc.frequency.setValueAtTime(180, now);
                osc.frequency.exponentialRampToValueAtTime(400, now + 0.18);

                gain.gain.setValueAtTime(0.3, now);
                gain.gain.exponentialRampToValueAtTime(0.01, now + 0.18);

                osc.connect(gain);
                gain.connect(this.ctx.destination);

                osc.start(now);
                osc.stop(now + 0.18);
            }

            playSlide() {
                if (!this.ctx) return;
                const now = this.ctx.currentTime;
                const bufferSize = this.ctx.sampleRate * 0.15;
                const buffer = this.ctx.createBuffer(1, bufferSize, this.ctx.sampleRate);
                const output = buffer.getChannelData(0);

                for (let i = 0; i < bufferSize; i++) {
                    output[i] = Math.random() * 2 - 1;
                }

                const whiteNoise = this.ctx.createBufferSource();
                whiteNoise.buffer = buffer;

                const filter = this.ctx.createBiquadFilter();
                filter.type = 'lowpass';
                filter.frequency.setValueAtTime(800, now);
                filter.frequency.exponentialRampToValueAtTime(200, now + 0.15);

                const gain = this.ctx.createGain();
                gain.gain.setValueAtTime(0.3, now);
                gain.gain.exponentialRampToValueAtTime(0.01, now + 0.15);

                whiteNoise.connect(filter);
                filter.connect(gain);
                gain.connect(this.ctx.destination);

                whiteNoise.start(now);
            }

            playCrash() {
                if (!this.ctx) return;
                const now = this.ctx.currentTime;
                const osc = this.ctx.createOscillator();
                const gain = this.ctx.createGain();

                osc.type = 'sawtooth';
                osc.frequency.setValueAtTime(120, now);
                osc.frequency.exponentialRampToValueAtTime(30, now + 0.4);

                gain.gain.setValueAtTime(0.5, now);
                gain.gain.exponentialRampToValueAtTime(0.01, now + 0.4);

                osc.connect(gain);
                gain.connect(this.ctx.destination);

                osc.start(now);
                osc.stop(now + 0.4);
            }
        }

        const sfx = new SoundEngine();

        // ==========================================
        // 2. GAME CONSTANTS & STATE
        // ==========================================
        const LANE_WIDTH = 3.2;
        const LANES = [-LANE_WIDTH, 0, LANE_WIDTH]; // Left (-1), Center (0), Right (1)
        const TILE_LENGTH = 20;
        const VISIBLE_TILES = 12;
        const INITIAL_SPEED = 22;
        const MAX_SPEED = 45;
        const ACCELERATION = 0.15;

        let gameState = {
            running: false,
            paused: false,
            score: 0,
            coins: 0,
            speed: INITIAL_SPEED,
            currentLane: 1, // 0: Left, 1: Center, 2: Right
            targetX: 0,
            posY: 0,
            jumpVelocity: 0,
            isJumping: false,
            isSliding: false,
            slideTimer: 0,
            highScore: localStorage.getItem('temple_run_hi_score') || 0
        };

        // ==========================================
        // 3. THREE.JS SETUP & TEXTURE GENERATORS
        // ==========================================
        const canvas = document.getElementById('game-canvas');
        const renderer = new THREE.WebGLRenderer({ canvas, antialias: true, powerPreference: "high-performance" });
        renderer.setSize(window.innerWidth, window.innerHeight);
        renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
        renderer.shadowMap.enabled = true;
        renderer.shadowMap.type = THREE.PCFSoftShadowMap;

        const scene = new THREE.Scene();
        scene.background = new THREE.Color(0x0a0812);
        scene.fog = new THREE.FogExp2(0x0a0812, 0.022);

        const camera = new THREE.PerspectiveCamera(60, window.innerWidth / window.innerHeight, 0.1, 150);

        // PROCEDURAL CANVAS TEXTURES
        function createStoneTexture() {
            const canvas = document.createElement('canvas');
            canvas.width = 256; canvas.height = 256;
            const ctx = canvas.getContext('2d');
            
            ctx.fillStyle = '#2a2421';
            ctx.fillRect(0, 0, 256, 256);

            // Brick Grid Pattern
            ctx.strokeStyle = '#15110f';
            ctx.lineWidth = 4;
            const rows = 8;
            const cols = 4;
            const rh = 256 / rows;
            const cw = 256 / cols;

            for (let i = 0; i < rows; i++) {
                ctx.beginPath();
                ctx.moveTo(0, i * rh);
                ctx.lineTo(256, i * rh);
                ctx.stroke();

                const offset = (i % 2) * (cw / 2);
                for (let j = 0; j <= cols + 1; j++) {
                    ctx.beginPath();
                    ctx.moveTo(j * cw - offset, i * rh);
                    ctx.lineTo(j * cw - offset, (i + 1) * rh);
                    ctx.stroke();
                }
            }

            // Noise overlay
            for (let i = 0; i < 1000; i++) {
                ctx.fillStyle = Math.random() > 0.5 ? 'rgba(255,255,255,0.03)' : 'rgba(0,0,0,0.08)';
                ctx.fillRect(Math.random() * 256, Math.random() * 256, 3, 3);
            }

            const texture = new THREE.CanvasTexture(canvas);
            texture.wrapS = THREE.RepeatWrapping;
            texture.wrapT = THREE.RepeatWrapping;
            return texture;
        }

        const stoneTexture = createStoneTexture();

        // ==========================================
        // 4. LIGHTING SETUP
        // ==========================================
        const ambientLight = new THREE.AmbientLight(0xffecd6, 0.4);
        scene.add(ambientLight);

        const dirLight = new THREE.DirectionalLight(0xfff5ea, 0.8);
        dirLight.position.set(20, 40, -10);
        dirLight.castShadow = true;
        dirLight.shadow.mapSize.width = 1024;
        dirLight.shadow.mapSize.height = 1024;
        dirLight.shadow.camera.near = 0.5;
        dirLight.shadow.camera.far = 80;
        dirLight.shadow.camera.left = -15;
        dirLight.shadow.camera.right = 15;
        dirLight.shadow.camera.top = 15;
        dirLight.shadow.camera.bottom = -15;
        scene.add(dirLight);

        // Player Point Light for dynamic glow
        const playerLight = new THREE.PointLight(0xffaa44, 1.2, 15);
        scene.add(playerLight);

        // ==========================================
        // 5. CHARACTER (RUNNER) MESH CREATION
        // ==========================================
        function createRunner() {
            const group = new THREE.Group();

            // Materials
            const skinMat = new THREE.MeshStandardMaterial({ color: 0xe0a96d, roughness: 0.6 });
            const shirtMat = new THREE.MeshStandardMaterial({ color: 0x2563eb, roughness: 0.5 }); // Blue explorer shirt
            const pantsMat = new THREE.MeshStandardMaterial({ color: 0x1e293b, roughness: 0.8 }); // Dark trousers
            const hairMat = new THREE.MeshStandardMaterial({ color: 0x3f2314, roughness: 0.9 });

            // Torso
            const torsoGeo = new THREE.BoxGeometry(0.7, 0.9, 0.4);
            const torso = new THREE.Mesh(torsoGeo, shirtMat);
            torso.position.y = 1.35;
            torso.castShadow = true;
            group.add(torso);

            // Head
            const headGeo = new THREE.BoxGeometry(0.45, 0.45, 0.45);
            const head = new THREE.Mesh(headGeo, skinMat);
            head.position.y = 2.1;
            head.castShadow = true;
            group.add(head);

            // Hair/Hat
            const hairGeo = new THREE.BoxGeometry(0.48, 0.15, 0.48);
            const hair = new THREE.Mesh(hairGeo, hairMat);
            hair.position.set(0, 2.35, 0);
            group.add(hair);

            // Legs
            const legGeo = new THREE.BoxGeometry(0.26, 0.8, 0.26);
            
            const leftLeg = new THREE.Mesh(legGeo, pantsMat);
            leftLeg.position.set(-0.2, 0.5, 0);
            leftLeg.castShadow = true;
            group.add(leftLeg);

            const rightLeg = new THREE.Mesh(legGeo, pantsMat);
            rightLeg.position.set(0.2, 0.5, 0);
            rightLeg.castShadow = true;
            group.add(rightLeg);

            // Arms
 
