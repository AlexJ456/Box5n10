document.addEventListener('DOMContentLoaded', () => {
    const app = document.getElementById('app-content');
    const canvas = document.getElementById('box-canvas');
    const ctx = canvas ? canvas.getContext('2d') : null;
    if (!app || !canvas || !ctx) {
        return;
    }
    const layoutHost = canvas.parentElement || document.querySelector('.container');
    const initialWidth = layoutHost ? layoutHost.clientWidth : canvas.clientWidth;
    const initialHeight = layoutHost ? layoutHost.clientHeight : canvas.clientHeight;

    const state = {
        isPlaying: false,
        count: 0,
        countdown: 4,
        totalTime: 0,
        soundEnabled: false,
        timeLimit: '',
        sessionComplete: false,
        timeLimitReached: false,
        phaseTime: 4,
        devicePixelRatio: Math.min(window.devicePixelRatio || 1, 1.75),
        viewportWidth: initialWidth,
        viewportHeight: initialHeight,
        prefersReducedMotion: false,
        hasStarted: false
    };

    const offlineNotification = document.getElementById('offline-notification');
    let wakeLock = null;
    let audioContext = new (window.AudioContext || window.webkitAudioContext)();

    const icons = {
        play: `<svg class="icon" viewBox="0 0 24 24"><polygon points="5 3 19 12 5 21 5 3"></polygon></svg>`,
        pause: `<svg class="icon" viewBox="0 0 24 24"><rect x="6" y="4" width="4" height="16"></rect><rect x="14" y="4" width="4" height="16"></rect></svg>`,
        volume2: `<svg class="icon" viewBox="0 0 24 24"><polygon points="11 5 6 9 2 9 2 15 6 15 11 19 11 5"></polygon><path d="M19.07 4.93a10 10 0 0 1 0 14.14M15.54 8.46a5 5 0 0 1 0 7.07"></path></svg>`,
        volumeX: `<svg class="icon" viewBox="0 0 24 24"><polygon points="11 5 6 9 2 9 2 15 6 15 11 19 11 5"></polygon><line x1="23" y1="9" x2="17" y2="15"></line><line x1="17" y1="9" x2="23" y2="15"></line></svg>`,
        rotateCcw: `<svg class="icon" viewBox="0 0 24 24"><polyline points="1 4 1 10 7 10"></polyline><path d="M3.51 15a9 9 0 1 0 2.13-9.36L1 10"></path></svg>`,
        clock: `<svg class="icon" viewBox="0 0 24 24"><circle cx="12" cy="12" r="10"></circle><polyline points="12 6 12 12 16 14"></polyline></svg>`
    };

    function getInstruction(count) {
        switch (count) {
            case 0: return 'Inhale';
            case 1: return 'Hold';
            case 2: return 'Exhale';
            case 3: return 'Wait';
            default: return '';
        }
    }

    const phaseColors = ['#f97316', '#fbbf24', '#38bdf8', '#22c55e'];

    function hexToRgba(hex, alpha) {
        const normalized = hex.replace('#', '');
        const bigint = parseInt(normalized, 16);
        const r = (bigint >> 16) & 255;
        const g = (bigint >> 8) & 255;
        const b = bigint & 255;
        return `rgba(${r}, ${g}, ${b}, ${alpha})`;
    }

    const toneFrequencies = [432, 528, 396, 639];

    let backgroundDots = [];
    let cachedGradient = null;
    let cachedGradientKey = '';

    function invalidateGradient() {
        cachedGradient = null;
        cachedGradientKey = '';
    }

    function regenerateDots() {
        backgroundDots = [];
        const width = state.viewportWidth;
        const height = state.viewportHeight;
        if (!width || !height) return;

        const baseSize = Math.min(width, height);
        const dotCount = Math.max(14, Math.floor(baseSize / 50));
        const palette = ['#f97316', '#fbbf24', '#38bdf8', '#22c55e', '#a855f7', '#f472b6'];

        for (let i = 0; i < dotCount; i++) {
            const radius = baseSize * (0.05 + Math.random() * 0.03);
            const color = palette[i % palette.length];
            const padding = radius + 16;
            let x, y;
            let attempts = 0;
            do {
                x = padding + Math.random() * (width - padding * 2);
                y = padding + Math.random() * (height - padding * 2);
                attempts++;
            } while (
                attempts < 25 &&
                backgroundDots.some(dot => {
                    const dx = dot.x - x;
                    const dy = dot.y - y;
                    const distance = Math.sqrt(dx * dx + dy * dy);
                    return distance < dot.radius + radius + 10;
                })
            );
            backgroundDots.push({ x, y, radius, color });
        }
    }

    function resizeCanvas() {
        const currentSizingElement = layoutHost || document.body;
        if (!currentSizingElement) {
            return;
        }

        const rect = currentSizingElement.getBoundingClientRect();
        const width = rect.width;
        const height = rect.height;
        const pixelRatio = Math.min(window.devicePixelRatio || 1, 1.75);

        state.viewportWidth = width;
        state.viewportHeight = height;
        state.devicePixelRatio = pixelRatio;

        canvas.style.width = `${width}px`;
        canvas.style.height = `${height}px`;
        canvas.width = Math.floor(width * pixelRatio);
        canvas.height = Math.floor(height * pixelRatio);

        if (ctx) {
            ctx.setTransform(pixelRatio, 0, 0, pixelRatio, 0, 0);
        }

        invalidateGradient();
        regenerateDots();

        drawScene();
    }

    window.addEventListener('resize', resizeCanvas, { passive: true });

    function updateMotionPreference(event) {
        state.prefersReducedMotion = event.matches;
        drawScene();
    }

    const motionQuery = typeof window.matchMedia === 'function'
        ? window.matchMedia('(prefers-reduced-motion: reduce)')
        : null;

    if (motionQuery) {
        state.prefersReducedMotion = motionQuery.matches;
        if (typeof motionQuery.addEventListener === 'function') {
            motionQuery.addEventListener('change', updateMotionPreference);
        } else if (typeof motionQuery.addListener === 'function') {
            motionQuery.addListener(updateMotionPreference);
        }
    }

    function formatTime(seconds) {
        const mins = Math.floor(seconds / 60);
        const secs = seconds % 60;
        return `${mins.toString().padStart(2, '0')}:${secs.toString().padStart(2, '0')}`;
    }

    function playTone(phaseIndex = 0) {
        if (!state.soundEnabled || !audioContext) return;
        try {
            const now = audioContext.currentTime;
            const oscillator = audioContext.createOscillator();
            const gainNode = audioContext.createGain();
            const filter = audioContext.createBiquadFilter();

            oscillator.type = 'sine';
            oscillator.frequency.setValueAtTime(
                toneFrequencies[phaseIndex % toneFrequencies.length] || 432,
                now
            );

            filter.type = 'lowpass';
            filter.frequency.setValueAtTime(1400, now);
            filter.Q.value = 0.9;

            gainNode.gain.setValueAtTime(0.0001, now);
            gainNode.gain.exponentialRampToValueAtTime(0.22, now + 0.05);
            gainNode.gain.exponentialRampToValueAtTime(0.0001, now + 0.5);

            oscillator.connect(filter);
            filter.connect(gainNode);
            gainNode.connect(audioContext.destination);

            oscillator.start(now);
            oscillator.stop(now + 0.55);
        } catch (e) {
            console.error('Error playing tone:', e);
        }
    }

    let interval;

    async function requestWakeLock() {
        if ('wakeLock' in navigator) {
            try {
                wakeLock = await navigator.wakeLock.request('screen');
                console.log('Wake lock is active');
            } catch (err) {
                console.error('Failed to acquire wake lock:', err);
            }
        } else {
            console.log('Wake Lock API not supported');
        }
    }

    function releaseWakeLock() {
        if (wakeLock !== null) {
            wakeLock.release()
                .then(() => {
                    wakeLock = null;
                    console.log('Wake lock released');
                })
                .catch(err => {
                    console.error('Failed to release wake lock:', err);
                });
        }
    }

    function togglePlay() {
        state.isPlaying = !state.isPlaying;
        if (state.isPlaying) {
            if (audioContext && audioContext.state === 'suspended') {
                audioContext.resume().then(() => {
                    console.log('AudioContext resumed');
                });
            }
            state.hasStarted = true;
            state.totalTime = 0;
            state.countdown = state.phaseTime;
            state.count = 0;
            state.sessionComplete = false;
            state.timeLimitReached = false;
            playTone(state.count);
            startInterval();
            regenerateDots();
            requestWakeLock();
        } else {
            clearInterval(interval);
            state.totalTime = 0;
            state.countdown = state.phaseTime;
            state.count = 0;
            state.sessionComplete = false;
            state.timeLimitReached = false;
            state.hasStarted = false;
            invalidateGradient();
            drawScene();
            releaseWakeLock();
        }
        render();
    }

    function resetToStart() {
        state.isPlaying = false;
        state.totalTime = 0;
        state.countdown = state.phaseTime;
        state.count = 0;
        state.sessionComplete = false;
        state.timeLimit = '';
        state.timeLimitReached = false;
        state.hasStarted = false;
        clearInterval(interval);
        invalidateGradient();
        drawScene();
        releaseWakeLock();
        render();
    }

    function toggleSound() {
        state.soundEnabled = !state.soundEnabled;
        render();
    }

    function handleTimeLimitChange(e) {
        state.timeLimit = e.target.value.replace(/[^0-9]/g, '');
    }

    function startWithPreset(minutes) {
        state.timeLimit = minutes.toString();
        state.isPlaying = true;
        state.totalTime = 0;
        state.countdown = state.phaseTime;
        state.count = 0;
        state.sessionComplete = false;
        state.timeLimitReached = false;
        state.hasStarted = true;
        if (audioContext && audioContext.state === 'suspended') {
            audioContext.resume().then(() => {
                console.log('AudioContext resumed');
            });
        }
        playTone(state.count);
        startInterval();
        regenerateDots();
        requestWakeLock();
        render();
    }

    function startInterval() {
        clearInterval(interval);
        interval = setInterval(() => {
            state.totalTime += 1;
            if (state.timeLimit && !state.timeLimitReached) {
                const timeLimitSeconds = parseInt(state.timeLimit) * 60;
                if (state.totalTime >= timeLimitSeconds) {
                    state.timeLimitReached = true;
                }
            }
            if (state.countdown === 1) {
                state.count = (state.count + 1) % 4;
                state.countdown = state.phaseTime;
                playTone(state.count);
                if (state.count === 3 && state.timeLimitReached) {
                    state.sessionComplete = true;
                    state.isPlaying = false;
                    state.hasStarted = false;
                    clearInterval(interval);
                    releaseWakeLock();
                }
            } else {
                state.countdown -= 1;
            }
            render();
        }, 1000);
    }

    function drawScene() {
        if (!ctx) return;

        const width = state.viewportWidth || canvas.clientWidth || canvas.width;
        const height = state.viewportHeight || canvas.clientHeight || canvas.height;
        if (!width || !height) {
            return;
        }

        const scale = state.devicePixelRatio || 1;
        ctx.save();
        ctx.setTransform(scale, 0, 0, scale, 0, 0);

        ctx.clearRect(0, 0, width, height);
        ctx.fillStyle = 'black';
        ctx.fillRect(0, 0, width, height);

        if (!state.isPlaying) {
            ctx.restore();
            return;
        }

        const gradientKey = `${Math.round(width)}-${Math.round(height)}`;
        if (!cachedGradient || cachedGradientKey !== gradientKey) {
            cachedGradient = ctx.createLinearGradient(0, 0, width, height);
            cachedGradient.addColorStop(0, hexToRgba('#0f172a', 0.9));
            cachedGradient.addColorStop(1, hexToRgba('#111827', 0.9));
            cachedGradientKey = gradientKey;
        }
        ctx.fillStyle = cachedGradient;
        ctx.fillRect(0, 0, width, height);

        if (!backgroundDots.length) {
            regenerateDots();
        }

        backgroundDots.forEach((dot, index) => {
            const glow = ctx.createRadialGradient(dot.x, dot.y, dot.radius * 0.25, dot.x, dot.y, dot.radius * 1.4);
            glow.addColorStop(0, hexToRgba(dot.color, 0.65));
            glow.addColorStop(1, hexToRgba(dot.color, 0));
            ctx.beginPath();
            ctx.fillStyle = glow;
            ctx.arc(dot.x, dot.y, dot.radius * 1.4, 0, Math.PI * 2);
            ctx.fill();

            const rim = ctx.createRadialGradient(dot.x, dot.y, 0, dot.x, dot.y, dot.radius * 1.05);
            rim.addColorStop(0, hexToRgba('#ffffff', 0.12));
            rim.addColorStop(0.6, hexToRgba(dot.color, 0.95));
            rim.addColorStop(1, hexToRgba(dot.color, 0.2));

            const offset = (index % 2 === 0 ? 1 : -1) * Math.min(dot.radius * 0.08, 3);
            ctx.beginPath();
            ctx.fillStyle = rim;
            ctx.arc(dot.x + offset, dot.y + offset, dot.radius * 1.05, 0, Math.PI * 2);
            ctx.fill();

            ctx.beginPath();
            ctx.fillStyle = dot.color;
            ctx.arc(dot.x + offset * 0.5, dot.y + offset * 0.5, dot.radius * 0.7, 0, Math.PI * 2);
            ctx.fill();
        });

        ctx.restore();
    }

    function updateCanvasVisibility() {
        const shouldShow = state.isPlaying;
        canvas.classList.toggle('is-visible', shouldShow);
    }

    function render() {
        let html = `
            <h1>Box Breathing</h1>
        `;
        if (state.isPlaying) {
            html += `
                <div class="timer">Total Time: ${formatTime(state.totalTime)}</div>
                <div class="instruction">${getInstruction(state.count)}</div>
            `;
            const phases = ['Inhale', 'Hold', 'Exhale', 'Wait'];
            html += `<div class="phase-tracker">`;
            phases.forEach((label, index) => {
                const phaseColor = phaseColors[index] || '#fde68a';
                const softPhaseColor = hexToRgba(phaseColor, 0.25);
                html += `
                    <div class="phase-item ${index === state.count ? 'active' : ''}" style="--phase-color: ${phaseColor}; --phase-soft: ${softPhaseColor};">
                        <span class="phase-dot"></span>
                        <span class="phase-label">${label}</span>
                    </div>
                `;
            });
            html += `</div>`;
        }
        if (state.timeLimitReached && !state.sessionComplete) {
            const limitMessage = state.isPlaying ? 'Finishing current cycle…' : 'Time limit reached';
            html += `<div class="limit-warning">${limitMessage}</div>`;
        }
        if (!state.isPlaying && !state.sessionComplete) {
            html += `
                <div class="settings">
                    <div class="form-group">
                        <label class="switch">
                            <input type="checkbox" id="sound-toggle" ${state.soundEnabled ? 'checked' : ''}>
                            <span class="slider"></span>
                        </label>
                        <label for="sound-toggle">
                            ${state.soundEnabled ? icons.volume2 : icons.volumeX}
                            Sound ${state.soundEnabled ? 'On' : 'Off'}
                        </label>
                    </div>
                    <div class="form-group">
                        <input
                            type="number"
                            inputmode="numeric"
                            placeholder="Time limit (minutes)"
                            value="${state.timeLimit}"
                            id="time-limit"
                            step="1"
                            min="0"
                        >
                        <label for="time-limit">Minutes (optional)</label>
                    </div>
                </div>
                <div class="prompt">Press start to begin</div>
            `;
        }
        if (state.sessionComplete) {
            html += `<div class="complete">Complete!</div>`;
        }
        if (!state.sessionComplete) {
            html += `
                <button id="toggle-play">
                    ${state.isPlaying ? icons.pause : icons.play}
                    ${state.isPlaying ? 'Pause' : 'Start'}
                </button>
            `;
        }
        if (!state.isPlaying && !state.sessionComplete) {
            html += `
                <div class="slider-container">
                    <label for="phase-time-slider">Phase Time (seconds): <span id="phase-time-value">${state.phaseTime}</span></label>
                    <input type="range" min="3" max="6" step="1" value="${state.phaseTime}" id="phase-time-slider">
                </div>
            `;
        }
        if (state.sessionComplete) {
            html += `
                <button id="reset">
                    ${icons.rotateCcw}
                    Back to Start
                </button>
            `;
        }
        if (!state.isPlaying && !state.sessionComplete) {
            html += `
                <div class="shortcut-buttons">
                    <button id="preset-2min" class="preset-button">
                        ${icons.clock} 2 min
                    </button>
                    <button id="preset-5min" class="preset-button">
                        ${icons.clock} 5 min
                    </button>
                    <button id="preset-10min" class="preset-button">
                        ${icons.clock} 10 min
                    </button>
                </div>
            `;
        }
        app.innerHTML = html;

        updateCanvasVisibility();

        if (!state.sessionComplete) {
            document.getElementById('toggle-play').addEventListener('click', togglePlay);
        }
        if (state.sessionComplete) {
            document.getElementById('reset').addEventListener('click', resetToStart);
        }
        if (!state.isPlaying && !state.sessionComplete) {
            document.getElementById('sound-toggle').addEventListener('change', toggleSound);
            const timeLimitInput = document.getElementById('time-limit');
            timeLimitInput.addEventListener('input', handleTimeLimitChange);
            const phaseTimeSlider = document.getElementById('phase-time-slider');
            phaseTimeSlider.addEventListener('input', function() {
                state.phaseTime = parseInt(this.value);
                document.getElementById('phase-time-value').textContent = state.phaseTime;
            });
            document.getElementById('preset-2min').addEventListener('click', () => startWithPreset(2));
            document.getElementById('preset-5min').addEventListener('click', () => startWithPreset(5));
            document.getElementById('preset-10min').addEventListener('click', () => startWithPreset(10));
        }
        drawScene();
    }

    function updateOfflineNotice() {
        if (!offlineNotification) return;
        offlineNotification.style.display = navigator.onLine ? 'none' : 'block';
    }

    window.addEventListener('online', updateOfflineNotice);
    window.addEventListener('offline', updateOfflineNotice);

    render();
    resizeCanvas();
    updateOfflineNotice();
});
