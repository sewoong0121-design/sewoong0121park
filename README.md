# <!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <meta name="apple-mobile-web-app-title" content="행운복권">
    <title>행운의 스크래치 복권</title>
    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            -webkit-tap-highlight-color: transparent;
            font-family: -apple-system, BlinkMacSystemFont, "SF Pro Display", "SF Pro Text", "Segoe UI", Roboto, sans-serif;
            user-select: none;
            -webkit-user-select: none;
        }

        body {
            background: linear-gradient(135deg, #1e3c72 0%, #2a5298 100%);
            min-height: 100vh;
            min-height: -webkit-fill-available;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: env(safe-area-inset-top, 20px) env(safe-area-inset-right, 15px) env(safe-area-inset-bottom, 20px) env(safe-area-inset-left, 15px);
            overflow-x: hidden;
            overflow-y: auto;
        }

        .container {
            background: rgba(255, 255, 255, 0.95);
            backdrop-filter: blur(20px);
            -webkit-backdrop-filter: blur(20px);
            padding: 24px 18px;
            border-radius: 24px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.3);
            max-width: 480px;
            width: 100%;
            text-align: center;
            position: relative;
            z-index: 2;
        }

        .header-row {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 4px;
        }

        h1 {
            color: #0f172a;
            font-size: 22px;
            font-weight: 800;
            letter-spacing: -0.5px;
        }

        .sound-btn {
            background: #f1f5f9;
            border: 1px solid #cbd5e1;
            padding: 6px 12px;
            border-radius: 20px;
            font-size: 13px;
            font-weight: 700;
            color: #334155;
            cursor: pointer;
            display: flex;
            align-items: center;
            gap: 4px;
            transition: all 0.2s;
        }

        .sound-btn:active {
            transform: scale(0.95);
        }

        .sound-btn.active {
            background: #dbeafe;
            border-color: #3b82f6;
            color: #1d4ed8;
        }

        .subtitle {
            color: #64748b;
            margin-bottom: 18px;
            font-size: 13px;
            font-weight: 500;
            text-align: left;
        }

        .scratch-card-wrapper {
            position: relative;
            width: 100%;
            height: 160px;
            border-radius: 18px;
            margin-bottom: 18px;
            overflow: hidden;
            box-shadow: inset 0 2px 6px rgba(0,0,0,0.1), 0 8px 20px rgba(0,0,0,0.15);
            background: #f1f5f9;
        }

        .scratch-result-layer {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
            background: linear-gradient(135deg, #fffbeb 0%, #fef3c7 100%);
            border: 3px solid #f59e0b;
            border-radius: 18px;
            padding: 10px;
        }

        .result-title {
            font-size: 24px;
            font-weight: 800;
            color: #b45309;
            margin-bottom: 4px;
            letter-spacing: -0.5px;
        }

        .result-desc {
            font-size: 13px;
            font-weight: 600;
            color: #92400e;
        }

        #scratchCanvas {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            cursor: pointer;
            touch-action: none;
            border-radius: 16px;
        }

        .action-row {
            display: flex;
            gap: 10px;
            margin-bottom: 18px;
        }

        .btn {
            flex: 1;
            padding: 14px;
            font-size: 15px;
            font-weight: 700;
            border: none;
            border-radius: 14px;
            cursor: pointer;
            transition: all 0.15s ease;
        }

        .btn:active:not(:disabled) {
            transform: scale(0.96);
        }

        .btn-generate {
            background: linear-gradient(135deg, #f59e0b 0%, #d97706 100%);
            color: white;
            box-shadow: 0 4px 14px rgba(217, 119, 6, 0.35);
        }

        .btn-auto-scratch {
            background: #e2e8f0;
            color: #334155;
        }

        .btn:disabled {
            background: #cbd5e1;
            color: #94a3b8;
            cursor: not-allowed;
            opacity: 0.7;
            box-shadow: none;
        }

        .stats-grid {
            display: grid;
            grid-template-columns: repeat(5, 1fr);
            gap: 6px;
            margin-bottom: 18px;
        }

        .stat-card {
            background: #f8fafc;
            padding: 8px 2px;
            border-radius: 10px;
            text-align: center;
            border: 1px solid #e2e8f0;
        }

        .stat-card.rank-1 { background: #fef3c7; border-color: #f59e0b; }
        .stat-card.rank-3 { background: #ffedd5; border-color: #f97316; }

        .stat-title {
            font-size: 11px;
            font-weight: 700;
            color: #475569;
            margin-bottom: 2px;
        }

        .stat-count {
            font-size: 12px;
            font-weight: 800;
            color: #0f172a;
        }

        .history-container {
            text-align: left;
            background: #f8fafc;
            padding: 12px;
            border-radius: 12px;
            max-height: 90px;
            overflow-y: auto;
            border: 1px solid #e2e8f0;
            margin-bottom: 18px;
        }

        .history-title {
            font-size: 11px;
            font-weight: 700;
            color: #475569;
            margin-bottom: 4px;
        }

        .history-list {
            font-size: 11px;
            color: #64748b;
            line-height: 1.4;
            font-weight: 500;
        }

        .reset-btn-container {
            width: 100%;
        }

        .btn-reset {
            background: #fee2e2;
            color: #dc2626;
            border: 1px solid #fca5a5;
            padding: 12px;
            font-size: 13px;
            font-weight: 700;
            border-radius: 12px;
            width: 100%;
            cursor: pointer;
            transition: background 0.15s;
        }

        .btn-reset:active {
            background: #fca5a5;
        }

        .modal-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0,0,0,0.4);
            backdrop-filter: blur(4px);
            -webkit-backdrop-filter: blur(4px);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 999;
            opacity: 0;
            pointer-events: none;
            transition: opacity 0.2s ease;
        }

        .modal-overlay.show {
            opacity: 1;
            pointer-events: auto;
        }

        .modal-box {
            background: rgba(255, 255, 255, 0.95);
            backdrop-filter: blur(20px);
            -webkit-backdrop-filter: blur(20px);
            padding: 22px;
            border-radius: 20px;
            width: 85%;
            max-width: 300px;
            text-align: center;
            box-shadow: 0 15px 30px rgba(0,0,0,0.25);
        }

        .modal-title {
            font-size: 17px;
            font-weight: 800;
            color: #0f172a;
            margin-bottom: 6px;
        }

        .modal-desc {
            font-size: 13px;
            color: #64748b;
            margin-bottom: 18px;
            line-height: 1.4;
        }

        .modal-btns {
            display: flex;
            gap: 8px;
        }

        .modal-btn {
            flex: 1;
            padding: 11px;
            border: none;
            border-radius: 10px;
            font-weight: 700;
            font-size: 14px;
            cursor: pointer;
        }

        .modal-btn-cancel {
            background: #e2e8f0;
            color: #475569;
        }

        .modal-btn-confirm {
            background: #dc2626;
            color: white;
        }

        #confettiCanvas {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            pointer-events: none;
            z-index: 998;
        }
    </style>
</head>
<body>

    <canvas id="confettiCanvas"></canvas>

    <div class="container">
        <div class="header-row">
            <h1>🎟️ 행운의 복권</h1>
            <button id="soundToggleBtn" class="sound-btn active" onclick="toggleSound()">
                <span id="soundIcon">🔊</span> <span id="soundText">소리 ON</span>
            </button>
        </div>
        <p class="subtitle" id="subtitleText">남은 총 수량: 150개 (새 복권을 뽑아주세요)</p>

        <div class="scratch-card-wrapper">
            <div class="scratch-result-layer">
                <div id="resultTitle" class="result-title">복권을 생성해주세요</div>
                <div id="resultDesc" class="result-desc">아래의 '새 복권 뽑기' 버튼 터치</div>
            </div>
            <canvas id="scratchCanvas"></canvas>
        </div>

        <div class="action-row">
            <button id="genBtn" class="btn btn-generate" onclick="generateNewTicket()">✨ 새 복권 뽑기</button>
            <button id="autoBtn" class="btn btn-auto-scratch" onclick="autoReveal()" disabled>⚡ 한 번에 긁기</button>
        </div>

        <div class="stats-grid">
            <div class="stat-card rank-1">
                <div class="stat-title">1등</div>
                <div id="count1" class="stat-count">2 / 2</div>
            </div>
            <div class="stat-card">
                <div class="stat-title">2등</div>
                <div id="count2" class="stat-count">4 / 4</div>
            </div>
            <div class="stat-card rank-3">
                <div class="stat-title">3등</div>
                <div id="count3" class="stat-count">20 / 20</div>
            </div>
            <div class="stat-card">
                <div class="stat-title">4등</div>
                <div id="count4" class="stat-count">50 / 50</div>
            </div>
            <div class="stat-card">
                <div class="stat-title">5등</div>
                <div id="count5" class="stat-count">74 / 74</div>
            </div>
        </div>

        <div class="history-container">
            <div class="history-title">최근 추첨 내역</div>
            <div id="historyList" class="history-list">아직 추첨 내역이 없습니다.</div>
        </div>

        <div class="reset-btn-container">
            <button class="btn-reset" onclick="showResetModal()">🔥 처음부터 다시 시작하기 (초기화)</button>
        </div>
    </div>

    <div id="resetModal" class="modal-overlay">
        <div class="modal-box">
            <div class="modal-title">초기화 확인</div>
            <div class="modal-desc">정말 처음부터 다시 시작하시겠습니까?<br>모든 기록과 수량이 리셋됩니다.</div>
            <div class="modal-btns">
                <button class="modal-btn modal-btn-cancel" onclick="hideResetModal()">취소</button>
                <button class="modal-btn modal-btn-confirm" onclick="executeReset()">초기화</button>
            </div>
        </div>
    </div>

    <script>
        // ⚙️ 총 150판 구성 및 80판 이후 1등 출현 로직
        const MIN_TURNS_FOR_1ST = 80;
        const initialPool = { 1: 2, 2: 4, 3: 20, 4: 50, 5: 74 };
        let currentPool = JSON.parse(JSON.stringify(initialPool));
        let totalRemaining = 150;
        let totalPlayed = 0;
        let history = [];
        
        let currentDrawnRank = null;
        let isTicketReady = false;
        let isRevealed = false;

        // 소리 설정 (iOS Web Audio API)
        let soundEnabled = true;
        let audioCtx = null;

        function initAudio() {
            if (!audioCtx) {
                const AudioContext = window.AudioContext || window.webkitAudioContext;
                if (AudioContext) {
                    audioCtx = new AudioContext();
                }
            }
            if (audioCtx && audioCtx.state === 'suspended') {
                audioCtx.resume();
            }
        }

        function toggleSound() {
            soundEnabled = !soundEnabled;
            const btn = document.getElementById('soundToggleBtn');
            const icon = document.getElementById('soundIcon');
            const text = document.getElementById('soundText');

            if (soundEnabled) {
                btn.classList.add('active');
                icon.innerText = '🔊';
                text.innerText = '소리 ON';
                initAudio();
            } else {
                btn.classList.remove('active');
                icon.innerText = '🔇';
                text.innerText = '소리 OFF';
            }
        }

        function playScratchSound() {
            if (!soundEnabled) return;
            initAudio();
            if (!audioCtx) return;

            try {
                const bufferSize = audioCtx.sampleRate * 0.05;
                const buffer = audioCtx.createBuffer(1, bufferSize, audioCtx.sampleRate);
                const data = buffer.getChannelData(0);
                for (let i = 0; i < bufferSize; i++) {
                    data[i] = Math.random() * 2 - 1;
                }

                const noise = audioCtx.createBufferSource();
                noise.buffer = buffer;

                const filter = audioCtx.createBiquadFilter();
                filter.type = 'bandpass';
                filter.frequency.value = 1000;

                const gain = audioCtx.createGain();
                gain.gain.setValueAtTime(0.15, audioCtx.currentTime);
                gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.05);

                noise.connect(filter);
                filter.connect(gain);
                gain.connect(audioCtx.destination);

                noise.start();
            } catch (e) {}
        }

        function playWinSound(rank) {
            if (!soundEnabled) return;
            initAudio();
            if (!audioCtx) return;

            try {
                const now = audioCtx.currentTime;
                const notes = rank <= 3 ? [523.25, 659.25, 783.99, 1046.50] : [440, 554.37];
                
                notes.forEach((freq, idx) => {
                    const osc = audioCtx.createOscillator();
                    const gain = audioCtx.createGain();
                    
                    osc.type = 'triangle';
                    osc.frequency.setValueAtTime(freq, now + idx * 0.1);
                    
                    gain.gain.setValueAtTime(0, now + idx * 0.1);
                    gain.gain.linearRampToValueAtTime(0.2, now + idx * 0.1 + 0.02);
                    gain.gain.exponentialRampToValueAtTime(0.001, now + idx * 0.1 + 0.3);
                    
                    osc.connect(gain);
                    gain.connect(audioCtx.destination);
                    
                    osc.start(now + idx * 0.1);
                    osc.stop(now + idx * 0.1 + 0.35);
                });
            } catch (e) {}
        }

        const canvas = document.getElementById('scratchCanvas');
        const ctx = canvas.getContext('2d');

        function initCanvas() {
            const dpr = window.devicePixelRatio || 1;
            const rect = canvas.getBoundingClientRect();

            canvas.width = rect.width * dpr;
            canvas.height = rect.height * dpr;
            ctx.scale(dpr, dpr);

            ctx.fillStyle = '#cbd5e1';
            ctx.fillRect(0, 0, rect.width, rect.height);
            
            ctx.font = 'bold 15px -apple-system, sans-serif';
            ctx.fillStyle = '#64748b';
            ctx.textAlign = 'center';
            ctx.textBaseline = 'middle';
            ctx.fillText('손가락으로 슥슥 긁어보세요! 🪙', rect.width / 2, rect.height / 2);
        }

        window.onload = initCanvas;
        window.onresize = () => { if(!isRevealed && isTicketReady) initCanvas(); };

        function generateNewTicket() {
            initAudio();
            if (totalRemaining <= 0) {
                alert("모든 복권이 소진되었습니다!");
                return;
            }

            totalPlayed++;

            // 80판 이하에서는 1등을 제한하는 로직
            let availableItems = [];
            for (let rank in currentPool) {
                const rankNum = parseInt(rank);
                if (rankNum === 1 && totalPlayed <= MIN_TURNS_FOR_1ST) {
                    continue; 
                }
                for (let i = 0; i < currentPool[rank]; i++) {
                    availableItems.push(rankNum);
                }
            }

            if (availableItems.length === 0) {
                for (let i = 0; i < currentPool[1]; i++) availableItems.push(1);
            }

            const randomIndex = Math.floor(Math.random() * availableItems.length);
            currentDrawnRank = availableItems[randomIndex];

            currentPool[currentDrawnRank]--;
            totalRemaining--;

            const titleEl = document.getElementById("resultTitle");
            const descEl = document.getElementById("resultDesc");

            if (currentDrawnRank === 1) {
                titleEl.innerText = "🥇 1등 당첨!";
                descEl.innerText = "대박 행운의 주인공!";
            } else if (currentDrawnRank === 2) {
                titleEl.innerText = "🥈 2등 당첨!";
                descEl.innerText = "멋진 상품 획득!";
            } else if (currentDrawnRank === 3) {
                titleEl.innerText = "🥉 3등 당첨!";
                descEl.innerText = "기분 좋은 3등!";
            } else if (currentDrawnRank === 4) {
                titleEl.innerText = "✨ 4등 당첨!";
                descEl.innerText = "소소한 행운!";
            } else {
                titleEl.innerText = "🍀 5등 당첨!";
                descEl.innerText = "다음 기회에 더 크게!";
            }

            isTicketReady = true;
            isRevealed = false;
            initCanvas();

            document.getElementById("genBtn").disabled = true;
            document.getElementById("autoBtn").disabled = false;
            document.getElementById("subtitleText").innerText = `남은 총 수량: ${totalRemaining}개 (긁는 중...)`;
        }

        let isDrawing = false;
        let lastSoundTime = 0;

        function getTouchPos(e) {
            const rect = canvas.getBoundingClientRect();
            const clientX = e.clientX || (e.touches && e.touches[0].clientX);
            const clientY = e.clientY || (e.touches && e.touches[0].clientY);
            return {
                x: clientX - rect.left,
                y: clientY - rect.top
            };
        }

        function scratch(e) {
            if (!isDrawing || !isTicketReady || isRevealed) return;
            if (e.cancelable) e.preventDefault();
            
            const pos = getTouchPos(e);

            ctx.globalCompositeOperation = 'destination-out';
            ctx.beginPath();
            ctx.arc(pos.x, pos.y, 28, 0, Math.PI * 2, false);
            ctx.fill();

            const now = Date.now();
            if (now - lastSoundTime > 60) {
                playScratchSound();
                lastSoundTime = now;
            }

            checkScratchProgress();
        }

        function startDrawing(e) {
            if (!isTicketReady || isRevealed) return;
            initAudio();
            isDrawing = true;
            scratch(e);
        }

        function stopDrawing() {
            isDrawing = false;
        }

        function checkScratchProgress() {
            const imageData = ctx.getImageData(0, 0, canvas.width, canvas.height);
            let transparentPixels = 0;
            const totalPixels = imageData.data.length / 4;

            for (let i = 3; i < imageData.data.length; i += 16) {
                if (imageData.data[i] === 0) transparentPixels++;
            }

            if ((transparentPixels * 4) / totalPixels > 0.4) {
                revealAll();
            }
        }

        function autoReveal() {
            if (!isTicketReady || isRevealed) return;
            initAudio();
            revealAll();
        }

        function revealAll() {
            if (isRevealed) return;
            isRevealed = true;
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            playWinSound(currentDrawnRank);

            if (currentDrawnRank <= 3) {
                triggerConfetti();
            }

            updateStatsAndHistory();
        }

        function updateStatsAndHistory() {
            document.getElementById("count1").innerText = `${currentPool[1]} / ${initialPool[1]}`;
            document.getElementById("count2").innerText = `${currentPool[2]} / ${initialPool[2]}`;
            document.getElementById("count3").innerText = `${currentPool[3]} / ${initialPool[3]}`;
            document.getElementById("count4").innerText = `${currentPool[4]} / ${initialPool[4]}`;
            document.getElementById("count5").innerText = `${currentPool[5]} / ${initialPool[5]}`;

            const now = new Date();
            const timeString = `${now.getHours().toString().padStart(2, '0')}:${now.getMinutes().toString().padStart(2, '0')}:${now.getSeconds().toString().padStart(2, '0')}`;
            history.unshift(`[${timeString}] ${currentDrawnRank}등 당첨`);
            if (history.length > 20) history.pop();

            document.getElementById("historyList").innerHTML = history.join("<br>");
            document.getElementById("subtitleText").innerText = `남은 총 수량: ${totalRemaining}개`;

            document.getElementById("genBtn").disabled = false;
            document.getElementById("autoBtn").disabled = true;

            if (totalRemaining === 0) {
                document.getElementById("genBtn").innerText = "모든 복권 소진!";
                document.getElementById("genBtn").disabled = true;
            }
        }

        canvas.addEventListener('mousedown', startDrawing);
        canvas.addEventListener('mousemove', scratch);
        window.addEventListener('mouseup', stopDrawing);

        canvas.addEventListener('touchstart', startDrawing, {passive: false});
        canvas.addEventListener('touchmove', scratch, {passive: false});
        window.addEventListener('touchend', stopDrawing);

        function showResetModal() {
            document.getElementById('resetModal').classList.add('show');
        }
        function hideResetModal() {
            document.getElementById('resetModal').classList.remove('show');
        }
        function executeReset() {
            hideResetModal();
            currentPool = JSON.parse(JSON.stringify(initialPool));
            totalRemaining = 150;
            totalPlayed = 0;
            history = [];
            isTicketReady = false;
            isRevealed = false;

            document.getElementById("genBtn").innerText = "✨ 새 복권 뽑기";
            document.getElementById("genBtn").disabled = false;
            document.getElementById("autoBtn").disabled = true;
            document.getElementById("resultTitle").innerText = "복권을 생성해주세요";
            document.getElementById("resultDesc").innerText = "아래의 '새 복권 뽑기' 버튼 터치";
            document.getElementById("subtitleText").innerText = "남은 총 수량: 150개 (새 복권을 뽑아주세요)";

            document.getElementById("count1").innerText = `2 / 2`;
            document.getElementById("count2").innerText = `4 / 4`;
            document.getElementById("count3").innerText = `20 / 20`;
            document.getElementById("count4").innerText = `50 / 50`;
            document.getElementById("count5").innerText = `74 / 74`;

            document.getElementById("historyList").innerText = "아직 추첨 내역이 없습니다.";
            initCanvas();
        }

        // 폭죽(Confetti) 애니메이션
        const confettiCanvas = document.getElementById('confettiCanvas');
        const cCtx = confettiCanvas.getContext('2d');
        let particles = [];

        function triggerConfetti() {
            confettiCanvas.width = window.innerWidth;
            confettiCanvas.height = window.innerHeight;
            particles = [];

            for (let i = 0; i < 110; i++) {
                particles.push({
                    x: confettiCanvas.width / 2,
                    y: confettiCanvas.height / 2,
                    vx: (Math.random() - 0.5) * 14,
                    vy: (Math.random() - 0.7) * 14,
                    size: Math.random() * 8 + 4,
                    color: ['#f59e0b', '#ef4444', '#3b82f6', '#10b981', '#8b5cf6'][Math.floor(Math.random() * 5)],
                    alpha: 1,
                    decay: Math.random() * 0.015 + 0.01
                });
            }
            animateConfetti();
        }

        function animateConfetti() {
            cCtx.clearRect(0, 0, confettiCanvas.width, confettiCanvas.height);
            let alive = false;

            for (let p of particles) {
                if (p.alpha > 0) {
                    alive = true;
                    p.x += p.vx;
                    p.y += p.vy;
                    p.vy += 0.38;
                    p.alpha -= p.decay;

                    cCtx.save();
                    cCtx.globalAlpha = Math.max(p.alpha, 0);
                    cCtx.fillStyle = p.color;
                    cCtx.beginPath();
                    cCtx.arc(p.x, p.y, p.size, 0, Math.PI * 2);
                    cCtx.fill();
                    cCtx.restore();
                }
            }

            if (alive) {
                requestAnimationFrame(animateConfetti);
            } else {
                cCtx.clearRect(0, 0, confettiCanvas.width, confettiCanvas.height);
            }
        }
    </script>
</body>
</html>
