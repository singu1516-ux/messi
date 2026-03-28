<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>12B11 - THE MASTERPIECE 2026</title>
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;500;800&family=Playfair+Display:italic,wght@700&display=swap" rel="stylesheet">
    
    <style>
        :root {
            --primary: #ff3344;
            --gold: #ffcc00;
            --bg: #030303;
            --glass: rgba(255, 255, 255, 0.03);
            --border: rgba(255, 255, 255, 0.1);
        }

        * { margin: 0; padding: 0; box-sizing: border-box; cursor: none; } /* Cursor tùy chỉnh cho Master */

        body {
            background: var(--bg);
            color: #fff;
            font-family: 'Plus Jakarta Sans', sans-serif;
            overflow-x: hidden;
        }

        /* Lớp phủ mở khóa âm thanh (BẮT BUỘC ĐỂ CÓ NHẠC) */
        #master-overlay {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: #000; z-index: 9999; display: flex; 
            flex-direction: column; align-items: center; justify-content: center;
            transition: 1.5s cubic-bezier(0.19, 1, 0.22, 1);
        }
        .enter-btn {
            background: none; border: 1px solid var(--primary); color: var(--primary);
            padding: 15px 40px; font-weight: 800; letter-spacing: 5px;
            cursor: pointer; transition: 0.5s; position: relative;
        }
        .enter-btn:hover { background: var(--primary); color: #fff; box-shadow: 0 0 50px var(--primary); }

        canvas { position: fixed; top: 0; left: 0; pointer-events: none; }
        #canvas-phuong { z-index: -2; opacity: 0.6; }
        #canvas-phaohoa { z-index: -1; }

        /* Giao diện chính */
        .master-container { position: relative; z-index: 10; }

        nav {
            padding: 30px 50px; display: flex; justify-content: space-between;
            mix-blend-mode: difference; position: fixed; width: 100%; z-index: 100;
        }

        .hero {
            height: 100vh; display: flex; flex-direction: column; 
            justify-content: center; align-items: center; text-align: center;
        }

        .class-title {
            font-size: clamp(5rem, 20vw, 15rem); font-weight: 800;
            line-height: 0.8; margin-bottom: 20px;
            background: linear-gradient(180deg, #fff, #555);
            -webkit-background-clip: text; -webkit-text-fill-color: transparent;
            filter: drop-shadow(0 20px 50px rgba(0,0,0,0.5));
        }

        .countdown { display: flex; gap: 20px; margin-top: 50px; }
        .unit {
            width: 100px; height: 120px; background: var(--glass);
            border: 1px solid var(--border); border-radius: 20px;
            display: flex; flex-direction: column; justify-content: center;
            backdrop-filter: blur(20px); transition: 0.5s;
        }
        .unit:hover { border-color: var(--primary); transform: translateY(-10px); }
        .num { font-size: 2.5rem; font-weight: 800; color: var(--primary); }
        .label { font-size: 0.6rem; text-transform: uppercase; letter-spacing: 2px; color: #888; }

        /* Custom Cursor */
        #cursor {
            width: 20px; height: 20px; border: 2px solid var(--primary);
            border-radius: 50%; position: fixed; pointer-events: none;
            z-index: 10000; transition: transform 0.1s;
        }

        .quote-section {
            padding: 200px 20px; text-align: center;
            font-family: 'Playfair Display', serif; font-size: 2rem; font-style: italic;
        }

        #music-control {
            position: fixed; bottom: 40px; right: 40px; z-index: 1000;
            width: 60px; height: 60px; border-radius: 50%; border: 1px solid var(--border);
            background: var(--glass); backdrop-filter: blur(10px);
            display: flex; align-items: center; justify-content: center; font-size: 24px;
        }
        
        .reveal { opacity: 0; transform: translateY(80px); transition: 1.5s cubic-bezier(0.19, 1, 0.22, 1); }
        .reveal.active { opacity: 1; transform: translateY(0); }
    </style>
</head>
<body>

    <div id="cursor"></div>

    <div id="master-overlay">
        <h2 style="font-weight: 300; letter-spacing: 10px; margin-bottom: 30px;">MEMORIES OF 12B11</h2>
        <button class="enter-btn" onclick="startMasterpiece()">BẮT ĐẦU</button>
    </div>

    <nav>
        <div style="font-weight: 800; letter-spacing: 3px;">12B11 LEGACY</div>
        <div>2023 // 2026</div>
    </nav>

    <div id="music-control" onclick="toggleMusic()">🎵</div>
    <div style="display:none"><div id="player"></div></div>

    <canvas id="canvas-phuong"></canvas>
    <canvas id="canvas-phaohoa"></canvas>

    <div class="master-container">
        <section class="hero">
            <h1 class="class-title reveal">12B11</h1>
            <p class="reveal" style="letter-spacing: 8px; color: var(--gold);">NIÊN KHÓA CỦA NHỮNG NHÀ VÔ ĐỊCH</p>
            
            <div class="countdown reveal">
                <div class="unit"><span class="num" id="d">00</span><span class="label">Ngày</span></div>
                <div class="unit"><span class="num" id="h">00</span><span class="label">Giờ</span></div>
                <div class="unit"><span class="num" id="m">00</span><span class="label">Phút</span></div>
                <div class="unit"><span class="num" id="s">00</span><span class="label">Giây</span></div>
            </div>
        </section>

        <section class="quote-section reveal">
            "Mình cùng nhau đóng băng trước khi tiếng ve kêu mờ dần... <br>
            Để những kỷ niệm này sẽ mãi là vô tận."
        </section>
    </div>

    <script src="https://www.youtube.com/iframe_api"></script>
    <script>
        // --- MASTER AUDIO SYSTEM ---
        let player;
        let isPlaying = false;

        function onYouTubeIframeAPIReady() {
            player = new YT.Player('player', {
                height: '0', width: '0', videoId: 'S_7M9U_Z6-I',
                events: { 'onReady': (e) => e.target.setVolume(100) }
            });
        }

        function startMasterpiece() {
            // Hiệu ứng biến mất màn hình chờ cực mượt
            document.getElementById('master-overlay').style.opacity = '0';
            setTimeout(() => {
                document.getElementById('master-overlay').style.display = 'none';
            }, 1500);

            // PHÁT NHẠC NGAY LẬP TỨC
            if (player) {
                player.playVideo();
                isPlaying = true;
                document.getElementById('music-control').innerText = '⏸️';
            }
        }

        function toggleMusic() {
            if(isPlaying) { player.pauseVideo(); document.getElementById('music-control').innerText = '🎵'; }
            else { player.playVideo(); document.getElementById('music-control').innerText = '⏸️'; }
            isPlaying = !isPlaying;
        }

        // --- CUSTOM CURSOR ---
        const cursor = document.getElementById('cursor');
        window.addEventListener('mousemove', (e) => {
            cursor.style.left = e.clientX + 'px';
            cursor.style.top = e.clientY + 'px';
        });

        // --- HOA PHƯỢNG RƠI (PRO VERSION) ---
        const canvasP = document.getElementById('canvas-phuong');
        const ctxP = canvasP.getContext('2d');
        let petals = [];
        window.addEventListener('resize', () => { canvasP.width = window.innerWidth; canvasP.height = window.innerHeight; });
        canvasP.width = window.innerWidth; canvasP.height = window.innerHeight;

        class Petal {
            constructor() { this.init(); }
            init() {
                this.x = Math.random() * canvasP.width;
                this.y = Math.random() * -canvasP.height;
                this.s = Math.random() * 3 + 2;
                this.vY = Math.random() * 1 + 1;
                this.vX = Math.random() * 2 - 1;
            }
            draw() {
                ctxP.fillStyle = '#ff3344';
                ctxP.beginPath();
                ctxP.arc(this.x, this.y, this.s, 0, Math.PI * 2);
                ctxP.fill();
                this.y += this.vY; this.x += this.vX;
                if(this.y > canvasP.height) this.init();
            }
        }
        for(let i=0; i<70; i++) petals.push(new Petal());
        function animP() { ctxP.clearRect(0,0,canvasP.width,canvasP.height); petals.forEach(p => p.draw()); requestAnimationFrame(animP); }
        animP();

        // --- ĐẾM NGƯỢC ---
        const target = new Date(2026, 5, 11).getTime();
        setInterval(() => {
            const now = new Date().getTime();
            const d = target - now;
            document.getElementById('d').innerText = Math.floor(d/(1000*60*60*24)).toString().padStart(2,'0');
            document.getElementById('h').innerText = Math.floor((d%(1000*60*60*24))/(1000*60*60)).toString().padStart(2,'0');
            document.getElementById('m').innerText = Math.floor((d%(1000*60*60))/(1000*60)).toString().padStart(2,'0');
            document.getElementById('s').innerText = Math.floor((d%(1000*60))/1000).toString().padStart(2,'0');
        }, 1000);

        // --- HIỆU ỨNG HIỆN DẦN ---
        const observer = new IntersectionObserver(entries => {
            entries.forEach(e => { if(e.isIntersecting) e.target.classList.add('active'); });
        });
        document.querySelectorAll('.reveal').forEach(el => observer.observe(el));
    </script>
</body>
</html>
