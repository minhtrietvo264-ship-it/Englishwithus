// These are all of my code in my index.html
// Path: app -> scr -> main -> assets -> index.html
// The collaboration between TPhuoc(SNH) and supporter is MTriet(HCMUS)
// The comments describe the main logic of the code for each feature to easily check after this app is completed

<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <meta name="theme-color" content="#020617">
    <meta name="mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-capable" content="yes">
    <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
    <title>Picabulary - App Học Từ Vựng</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Manrope:wght@400;500;600;700;800&family=Fraunces:opsz,wght@9..144,500;9..144,600&display=swap" rel="stylesheet">
    <style>
        :root {
          --radius-card: 18px;
          --radius-pill: 999px;
          --shadow-soft: 0 10px 30px -12px rgba(0,0,0,0.35);
          --shadow-lift: 0 16px 40px -14px rgba(79,70,229,0.45);
          --ease-out: cubic-bezier(0.16, 1, 0.3, 1);
          --ease-standard: cubic-bezier(0.4, 0, 0.2, 1);
        }

        * { font-family: 'Manrope', system-ui, sans-serif; }
        .font-display { font-family: 'Fraunces', serif; font-feature-settings: 'ss01'; }

        /* --- Full-bleed app shell: fills the REAL device screen, no fake phone chrome --- */
        html, body {
          height: 100%;
          margin: 0;
          overscroll-behavior: none;
          background: #0f172a;
        }
        #appShell {
          height: 100dvh;
          width: 100%;
        }

        @keyframes scanAnimation {
          0% { top: 10%; opacity: 0.8; }
          50% { top: 85%; opacity: 1; }
          100% { top: 10%; opacity: 0.8; }
        }
        .scanner-line {
          position: absolute;
          left: 5%;
          right: 5%;
          height: 3px;
          background: linear-gradient(90deg, transparent, #6366f1, #a855f7, transparent);
          box-shadow: 0 0 12px #6366f1;
          animation: scanAnimation 2.2s ease-in-out infinite;
        }
        .word-token { transition: all 0.2s var(--ease-standard); cursor: pointer; }
        .word-token.highlighted {
          background-color: #fef08a; color: #854d0e; border-radius: 4px;
          padding: 1px 3px; box-shadow: 0 1px 3px rgba(0,0,0,0.1);
        }
        .perspective { perspective: 1200px; }
        .preserve-3d { transform-style: preserve-3d; transition: transform 0.55s var(--ease-out); }
        .backface-hidden { backface-visibility: hidden; }
        .rotate-y-180 { transform: rotateY(180deg); }

        /* --- Screen transition system --- */
        .app-screen {
          opacity: 1;
          transform: translateY(0) scale(1);
          transition: opacity 0.28s var(--ease-out), transform 0.28s var(--ease-out);
        }
        .app-screen.screen-hidden {
          display: none;
        }
        .app-screen.screen-leaving {
          opacity: 0;
          transform: translateY(6px) scale(0.99);
        }
        .app-screen.screen-entering {
          opacity: 0;
          transform: translateY(8px) scale(0.99);
        }

        /* --- Shared interactive feedback --- */
        .press { transition: transform 0.15s var(--ease-standard), box-shadow 0.15s var(--ease-standard), border-color 0.15s var(--ease-standard), background-color 0.15s var(--ease-standard); }
        .press:active { transform: scale(0.96); }

        /* --- Quiz option stagger-in --- */
        @keyframes optionRise {
          from { opacity: 0; transform: translateY(10px); }
          to { opacity: 1; transform: translateY(0); }
        }
        .option-rise { animation: optionRise 0.34s var(--ease-out) both; }

        /* --- Toast --- */
        @keyframes toastIn {
          from { opacity: 0; transform: translate(-50%, 10px) scale(0.96); }
          to { opacity: 1; transform: translate(-50%, 0) scale(1); }
        }
        @keyframes toastOut {
          from { opacity: 1; transform: translate(-50%, 0) scale(1); }
          to { opacity: 0; transform: translate(-50%, 8px) scale(0.97); }
        }
        .toast-in { animation: toastIn 0.32s var(--ease-out) forwards; }
        .toast-out { animation: toastOut 0.22s var(--ease-standard) forwards; }

        /* --- Modal --- */
        @keyframes modalIn {
          from { opacity: 0; transform: scale(0.95) translateY(6px); }
          to { opacity: 1; transform: scale(1) translateY(0); }
        }
        .modal-in { animation: modalIn 0.26s var(--ease-out) both; }

        /* --- Confetti (single orchestrated moment on quiz completion) --- */
        @keyframes confettiFall {
          0% { transform: translateY(-10px) rotate(0deg); opacity: 1; }
          100% { transform: translateY(220px) rotate(360deg); opacity: 0; }
        }
        .confetti-piece { position: absolute; top: 0; border-radius: 2px; animation: confettiFall 1.6s var(--ease-standard) forwards; }

        /* --- Progress bar smoothing --- */
        .progress-fill { transition: width 0.45s var(--ease-out); }

        /* --- Scrollbars --- */
        .thin-scroll::-webkit-scrollbar { width: 4px; }
        .thin-scroll::-webkit-scrollbar-thumb { background: #475569; border-radius: 4px; }
        .thin-scroll::-webkit-scrollbar-track { background: transparent; }

        /* --- Accessible focus --- */
        button:focus-visible, input:focus-visible {
          outline: 2px solid #818cf8;
          outline-offset: 2px;
        }

        @media (prefers-reduced-motion: reduce) {
          *, *::before, *::after { animation-duration: 0.01ms !important; transition-duration: 0.01ms !important; }
        }

        /* --- Streak flame (TikTok-style: purple + shake when studied today) --- */
        @keyframes flameShake {
          0%, 100% { transform: rotate(0deg) scale(1); }
          15% { transform: rotate(-8deg) scale(1.06); }
          30% { transform: rotate(6deg) scale(0.97); }
          45% { transform: rotate(-5deg) scale(1.05); }
          60% { transform: rotate(4deg) scale(0.99); }
          75% { transform: rotate(-2deg) scale(1.02); }
        }
        .streak-flame {
          transition: filter 0.3s var(--ease-standard);
        }
        .streak-flame.is-active {
          animation: flameShake 2.6s var(--ease-standard) infinite;
          filter: drop-shadow(0 0 7px rgba(168, 85, 247, 0.85));
        }
        .streak-flame.is-inactive {
          filter: grayscale(1) opacity(0.45);
        }

        /* --- Flashcard face depth (fills the fix for the collapsed h-76 card) --- */
        .fc-face {
          box-shadow: inset 0 1px 0 rgba(255,255,255,0.06), 0 20px 40px -18px rgba(0,0,0,0.55);
        }
        .fc-glow {
          position: absolute;
          top: -40%;
          left: 50%;
          width: 260px;
          height: 260px;
          transform: translateX(-50%);
          background: radial-gradient(circle, rgba(99,102,241,0.22) 0%, transparent 70%);
          pointer-events: none;
        }
        .fc-glow-back {
          background: radial-gradient(circle, rgba(16,185,129,0.16) 0%, transparent 70%);
        }
        @keyframes flipHintPulse {
          0%, 100% { transform: rotate(0deg); opacity: 0.85; }
          50% { transform: rotate(180deg); opacity: 1; }
        }
        .flip-hint { animation: flipHintPulse 2.4s var(--ease-standard) infinite; }
    </style>
</head>
<body class="bg-slate-900 text-slate-100 antialiased">

<!-- APP SHELL: this fills the real device screen edge-to-edge. No demo wrapper, no fake phone bezel. -->
<div id="appShell" class="bg-slate-950 flex flex-col overflow-hidden relative select-none">

    <!-- APP BODY -->
    <div class="flex-1 flex flex-col bg-slate-900 overflow-hidden relative" style="padding-top: env(safe-area-inset-top, 0px);">

        <!-- 1. TAB HOME -->
        <div id="tabHome" class="app-screen flex-1 flex flex-col overflow-y-auto thin-scroll p-4 space-y-4">
            <div class="flex items-center justify-between">
                <div>
                    <h2 class="font-display text-xl font-semibold text-white tracking-tight">Picabulary</h2>
                    <p class="text-[11px] text-slate-400">Song ngữ Anh - Việt &amp; Chuẩn IPA</p>
                </div>
                <div id="streakBadge" class="flex items-center gap-1.5 px-3 py-1 rounded-full text-xs font-bold transition-colors">
                    <svg id="streakFlame" class="w-4 h-4 streak-flame" viewBox="0 0 24 24" fill="currentColor">
                        <path d="M12.963 2.286a.75.75 0 00-1.071-.136 9.742 9.742 0 00-3.539 6.176 7.547 7.547 0 01-1.705-1.715.75.75 0 00-1.152-.082A9 9 0 1015.68 4.534a7.46 7.46 0 01-2.717-2.248zM15.75 14.25a3.75 3.75 0 11-7.313-1.172c.628.465 1.35.81 2.133 1.005a5.981 5.981 0 011.925-3.545 3.75 3.75 0 013.255 3.712z"/>
                    </svg>
                    <span id="streakDays">5 ngày</span>
                </div>
            </div>

            <div class="bg-gradient-to-br from-slate-800 to-slate-850 border border-slate-700/80 rounded-2xl p-4 shadow-sm space-y-3">
                <div class="flex items-center justify-between">
                    <div>
                        <div class="text-xs font-bold text-white">Mục tiêu hôm nay</div>
                        <div class="text-[11px] text-slate-400">Đã học 6/10 từ vựng</div>
                    </div>
                    <button onclick="startQuizMode()" class="press px-3 py-1.5 bg-emerald-600 hover:bg-emerald-500 text-white rounded-xl text-xs font-bold shadow">
                        Luyện Quiz ngay
                    </button>
                </div>
                <div class="w-full bg-slate-700 h-2 rounded-full overflow-hidden">
                    <div class="progress-fill bg-emerald-500 h-full rounded-full" style="width: 60%"></div>
                </div>
            </div>

            <div onclick="switchAppTab('scan')" class="press bg-gradient-to-r from-indigo-600 via-indigo-700 to-purple-700 rounded-2xl p-4 text-white shadow-lg cursor-pointer hover:opacity-95">
                <div class="flex items-center justify-between">
                    <div class="space-y-1">
                        <span class="text-[9px] bg-white/20 px-2 py-0.5 rounded font-bold uppercase tracking-wider">Nạp từ mới</span>
                        <h3 class="text-base font-black">Quét trang sách giấy</h3>
                        <p class="text-[11px] text-indigo-100/90 leading-tight">Chụp ảnh OCR hoặc dùng Bút gạch chân từ cần học</p>
                    </div>
                    <div class="w-11 h-11 rounded-full bg-white/20 flex items-center justify-center flex-shrink-0">
                        <svg class="w-6 h-6 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 9a2 2 0 012-2h.93a2 2 0 001.664-.89l.812-1.22A2 2 0 0110.07 4h3.86a2 2 0 011.664.89l.812 1.22A2 2 0 0018.07 7H19a2 2 0 012 2v9a2 2 0 01-2 2H5a2 2 0 01-2-2V9z"></path><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 13a3 3 0 11-6 0 3 3 0 016 0z"></path></svg>
                    </div>
                </div>
            </div>

            <div class="space-y-2">
                <div class="text-xs font-bold text-slate-300 uppercase tracking-wide">Trung tâm Ôn Luyện</div>
                <div class="grid grid-cols-2 gap-2.5">
                    <div onclick="startQuizMode()" class="press bg-slate-800/80 border border-emerald-500/40 hover:border-emerald-500 rounded-xl p-3 cursor-pointer flex flex-col justify-between h-[5.5rem] shadow-sm">
                        <svg class="w-5 h-5 text-emerald-400" fill="none" stroke="currentColor" viewBox="0 0 24 24"><circle cx="12" cy="12" r="8" stroke-width="2"/><circle cx="12" cy="12" r="4" stroke-width="2"/><circle cx="12" cy="12" r="0.7" fill="currentColor"/></svg>
                        <div>
                            <div class="text-xs font-bold text-emerald-300">Luyện Trắc nghiệm</div>
                            <div class="text-[10px] text-slate-400">Quiz 4 đáp án</div>
                        </div>
                    </div>

                    <div onclick="switchAppTab('flashcards')" class="press bg-slate-800/80 border border-slate-700 hover:border-indigo-500/50 rounded-xl p-3 cursor-pointer flex flex-col justify-between h-[5.5rem]">
                        <svg class="w-5 h-5 text-indigo-400" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 11H5m14 0a2 2 0 012 2v6a2 2 0 01-2 2H5a2 2 0 01-2-2v-6a2 2 0 012-2m14 0V9a2 2 0 00-2-2M5 11V9a2 2 0 012-2m0 0V5a2 2 0 012-2h6a2 2 0 012 2v2M7 7h10"/></svg>
                        <div>
                            <div class="text-xs font-bold text-white">Luyện Flashcard</div>
                            <div class="text-[10px] text-slate-400">IPA &amp; Nghĩa tiếng Việt</div>
                        </div>
                    </div>

                    <div onclick="startListeningMode()" class="press bg-slate-800/80 border border-purple-500/40 hover:border-purple-500 rounded-xl p-3 cursor-pointer flex flex-col justify-between h-[5.5rem]">
                        <svg class="w-5 h-5 text-purple-400" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 18v-6a9 9 0 0118 0v6"/><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 19a2 2 0 01-2 2h-1a2 2 0 01-2-2v-3a2 2 0 012-2h3v5zM3 19a2 2 0 002 2h1a2 2 0 002-2v-3a2 2 0 00-2-2H3v5z"/></svg>
                        <div>
                            <div class="text-xs font-bold text-purple-300">Thử thách Nghe</div>
                            <div class="text-[10px] text-slate-400">Giọng chuẩn UK</div>
                        </div>
                    </div>

                    <div onclick="switchAppTab('wordbank')" class="press bg-slate-800/80 border border-slate-700 hover:border-teal-500/50 rounded-xl p-3 cursor-pointer flex flex-col justify-between h-[5.5rem]">
                        <svg class="w-5 h-5 text-teal-400" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 6.253v13m0-13C10.832 5.477 9.246 5 7.5 5S4.168 5.477 3 6.253v13C4.168 18.477 5.754 18 7.5 18s3.332.477 4.5 1.253m0-13C13.168 5.477 14.754 5 16.5 5c1.747 0 3.332.477 4.5 1.253v13C19.832 18.477 18.247 18 16.5 18c-1.746 0-3.332.477-4.5 1.253"/></svg>
                        <div>
                            <div class="text-xs font-bold text-white">Ngân hàng từ</div>
                            <div class="text-[10px] text-slate-400" id="homeVocabCount">8 từ đã lưu</div>
                        </div>
                    </div>
                </div>
            </div>

            <div class="space-y-2 pt-1 pb-4">
                <div class="flex items-center justify-between text-xs">
                    <span class="font-bold text-slate-300 uppercase tracking-wide">Từ vựng hôm nay</span>
                    <span onclick="switchAppTab('wordbank')" class="text-indigo-400 hover:underline cursor-pointer">Xem tất cả →</span>
                </div>
                <div id="homeWordPreviewList" class="space-y-2"></div>
            </div>
        </div>

        <!-- 2. MÀN HÌNH QUIZ -->
        <div id="screenQuizView" class="app-screen screen-hidden flex-1 flex flex-col bg-slate-900 justify-between overflow-hidden">
            <div class="p-3 bg-slate-950 flex items-center justify-between border-b border-slate-800">
                <button onclick="switchAppTab('home')" class="press text-slate-400 hover:text-white text-xs flex items-center gap-1">
                    <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path></svg>
                </button>
                <span id="quizViewCounter" class="text-xs font-bold text-indigo-400">Câu 1 / 8</span>
                <span id="quizViewScore" class="text-xs font-semibold text-emerald-400 bg-emerald-500/10 px-2 py-0.5 rounded">0 Điểm</span>
            </div>

            <div class="w-full bg-slate-800 h-1">
                <div id="quizViewProgressBar" class="progress-fill bg-indigo-500 h-full" style="width: 12%"></div>
            </div>

            <div class="flex-1 p-4 flex flex-col justify-between overflow-y-auto thin-scroll">
                <div class="space-y-3">
                    <div class="bg-gradient-to-br from-indigo-950 to-slate-800 border border-indigo-500/40 rounded-2xl p-4 text-center shadow-lg relative">
                        <span class="text-[9px] uppercase font-bold text-indigo-300 tracking-wider">Chọn nghĩa đúng của từ</span>

                        <div class="flex items-center justify-center gap-2 mt-1">
                            <h2 id="quizTargetWord" class="text-2xl font-black text-white uppercase tracking-wide">EPHEMERAL</h2>
                            <button onclick="speakUK(document.getElementById('quizTargetWord').innerText)" class="press w-8 h-8 rounded-full bg-indigo-600 hover:bg-indigo-500 text-white flex items-center justify-center shadow" title="Phát âm UK">
                                <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15.536 8.464a5 5 0 010 7.072m2.828-9.9a9 9 0 010 12.728M5.586 15H4a1 1 0 01-1-1v-4a1 1 0 011-1h1.586l4.707-4.707C10.923 3.663 12 4.109 12 5v14c0 .891-1.077 1.337-1.707.707L5.586 15z"></path></svg>
                            </button>
                        </div>

                        <div id="quizTargetIPA" class="text-xs font-semibold text-indigo-300 font-mono mt-0.5">/ɪˈfem.ər.əl/</div>
                    </div>

                    <div id="quizOptionsContainer" class="space-y-2"></div>
                </div>

                <div class="pt-2">
                    <button id="btnQuizNext" onclick="nextQuizQuestion()" class="press hidden w-full py-2.5 bg-indigo-600 hover:bg-indigo-500 text-white font-bold text-xs rounded-xl shadow-lg">
                        Câu tiếp theo →
                    </button>
                </div>
            </div>
        </div>

        <!-- 3. TỔNG KẾT QUIZ -->
        <div id="screenQuizSummary" class="app-screen screen-hidden flex-1 flex flex-col bg-slate-900 p-5 items-center justify-between text-center overflow-y-auto thin-scroll relative">
            <div id="confettiLayer" class="absolute inset-0 pointer-events-none overflow-hidden"></div>
            <div class="space-y-3 pt-3 w-full">
                <div class="w-16 h-16 bg-emerald-500/20 text-emerald-400 rounded-full flex items-center justify-center mx-auto border-2 border-emerald-500/40">
                    <svg class="w-8 h-8" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z"></path></svg>
                </div>
                <h2 class="font-display text-lg font-semibold text-white">Hoàn Thành Bài Tập!</h2>
                <p id="quizSummaryScoreText" class="text-xs text-slate-300 font-medium">Đúng 8/8 câu (100%)</p>

                <div class="bg-slate-800/90 border border-slate-700 rounded-xl p-3 text-left text-xs space-y-2 max-h-48 overflow-y-auto thin-scroll">
                    <div class="font-bold text-indigo-300 text-[11px] flex items-center justify-between">
                        <span>Từ vựng vừa ôn:</span>
                        <span class="text-[9px] text-slate-400">Bấm 🔊 để nghe lại</span>
                    </div>
                    <div id="quizSummaryList" class="space-y-1.5 text-slate-300"></div>
                </div>
            </div>

            <div class="w-full pb-2">
                <button onclick="switchAppTab('home')" class="press w-full py-2.5 bg-indigo-600 hover:bg-indigo-500 text-white font-bold text-xs rounded-xl shadow">
                    Quay về Trang chủ
                </button>
            </div>
        </div>

        <!-- 4. THỬ THÁCH NGHE -->
        <div id="screenListeningView" class="app-screen screen-hidden flex-1 flex flex-col bg-slate-900 justify-between p-4 overflow-hidden">
            <div class="flex items-center justify-between border-b border-slate-800 pb-2 text-xs">
                <button onclick="switchAppTab('home')" class="press text-slate-400 hover:text-white">✕ Thoát</button>
                <span class="font-bold text-purple-300">Thử thách Nghe UK</span>
                <span id="listeningScoreBadge" class="text-emerald-400 font-bold">0 Điểm</span>
            </div>

            <div class="flex flex-col items-center justify-center space-y-3 py-4">
                <p class="text-xs text-slate-300 text-center">Nghe phát âm chuẩn Oxford và chọn từ đúng:</p>

                <button onclick="playCurrentListeningWord()" class="press w-20 h-20 rounded-full bg-gradient-to-br from-purple-600 to-indigo-600 hover:scale-105 text-white flex items-center justify-center shadow-xl shadow-purple-600/30">
                    <svg class="w-10 h-10" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15.536 8.464a5 5 0 010 7.072m2.828-9.9a9 9 0 010 12.728M5.586 15H4a1 1 0 01-1-1v-4a1 1 0 011-1h1.586l4.707-4.707C10.923 3.663 12 4.109 12 5v14c0 .891-1.077 1.337-1.707.707L5.586 15z"></path></svg>
                </button>
                <span class="text-[10px] text-slate-400">Chạm để nghe lại phát âm</span>
            </div>

            <div id="listeningOptionsContainer" class="space-y-2"></div>

            <div class="pt-2">
                <button id="btnListeningNext" onclick="nextListeningQuestion()" class="press hidden w-full py-2.5 bg-purple-600 hover:bg-purple-500 text-white font-bold text-xs rounded-xl shadow">
                    Câu tiếp theo →
                </button>
            </div>
        </div>

        <!-- 5. FLASHCARDS -->
        <div id="tabFlashcards" class="app-screen screen-hidden flex-1 flex flex-col p-4">
            <div class="flex items-center justify-between text-xs">
                <button onclick="switchAppTab('home')" class="press text-slate-400 hover:text-white flex items-center gap-1">
                    <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 19l-7-7 7-7"></path></svg> Trang chủ
                </button>
                <span id="fcCounter" class="font-bold text-indigo-400">Thẻ 1 / 8</span>
            </div>

            <div class="w-full bg-slate-800 h-1.5 rounded-full overflow-hidden mt-3">
                <div id="fcProgressBar" class="progress-fill bg-indigo-500 h-full" style="width: 12%"></div>
            </div>

            <div class="perspective flex-1 w-full cursor-pointer mt-4 min-h-[230px]" onclick="flipFlashcard()">
                <div id="flashcardInner" class="preserve-3d relative w-full h-full">
                    <div class="fc-face backface-hidden absolute inset-0 bg-gradient-to-br from-slate-800 to-slate-850 border border-slate-700/80 rounded-3xl p-5 flex flex-col items-center justify-center text-center shadow-xl overflow-hidden">
                        <div class="fc-glow"></div>
                        <span class="text-[9px] text-indigo-300 bg-indigo-500/20 px-2.5 py-0.5 rounded-full font-semibold mb-4 flex items-center gap-1.5">
              <svg class="w-2.5 h-2.5 flip-hint" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.5" d="M4 4v5h5M20 20v-5h-5M4 9a9 9 0 0114.13-5.36M20 15a9 9 0 01-14.13 5.36"/></svg>
              Chạm để xem Nghĩa Tiếng Việt
            </span>
                        <h2 id="fcFrontWord" class="text-3xl font-black text-white uppercase tracking-wider">EPHEMERAL</h2>
                        <span id="fcFrontIPA" class="text-sm font-semibold text-indigo-400 mt-1.5">/ɪˈfem.ər.əl/</span>

                        <button onclick="event.stopPropagation(); speakUK(document.getElementById('fcFrontWord').innerText)" class="press mt-5 w-11 h-11 rounded-full bg-indigo-600 hover:bg-indigo-500 text-white flex items-center justify-center shadow-lg shadow-indigo-600/30">
                            <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15.536 8.464a5 5 0 010 7.072m2.828-9.9a9 9 0 010 12.728M5.586 15H4a1 1 0 01-1-1v-4a1 1 0 011-1h1.586l4.707-4.707C10.923 3.663 12 4.109 12 5v14c0 .891-1.077 1.337-1.707.707L5.586 15z"></path></svg>
                        </button>
                        <span class="text-[9px] text-slate-400 mt-2">Phát âm Oxford (UK)</span>
                    </div>

                    <div class="fc-face rotate-y-180 backface-hidden absolute inset-0 bg-gradient-to-br from-indigo-950 to-slate-900 border border-indigo-500/40 rounded-3xl p-5 flex flex-col items-center justify-center text-center shadow-xl overflow-hidden">
                        <div class="fc-glow fc-glow-back"></div>
                        <span class="text-[9px] text-emerald-300 bg-emerald-500/20 px-2.5 py-0.5 rounded-full font-semibold mb-3">
              Nghĩa Tiếng Việt
            </span>
                        <h3 id="fcBackVietnamese" class="text-lg font-bold text-white leading-snug">
                            phù du, ngắn ngủi, sớm nở tối tàn
                        </h3>
                        <p id="fcBackDef" class="text-xs text-slate-300 mt-2.5 leading-relaxed italic">
                            lasting for a very short time; fleeting
                        </p>
                        <div class="mt-4 px-3 py-2 bg-slate-800/70 border border-slate-700/60 rounded-xl text-[11px] text-slate-300">
                            "<span id="fcBackExample">Momentary thoughts are ephemeral.</span>"
                        </div>
                    </div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-3 pt-4">
                <button onclick="handleFlashcardAction(false)" class="press py-2.5 rounded-xl border border-rose-500/60 bg-rose-500/10 text-rose-300 text-xs font-bold hover:bg-rose-500/20 flex items-center justify-center gap-1.5">
                    <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/></svg>
                    Chưa nhớ
                </button>
                <button onclick="handleFlashcardAction(true)" class="press py-2.5 rounded-xl bg-emerald-600 hover:bg-emerald-500 text-white text-xs font-bold shadow-lg flex items-center justify-center gap-1.5">
                    <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M5 13l4 4L19 7"/></svg>
                    Đã thuộc
                </button>
            </div>
        </div>

        <!-- 6. NGÂN HÀNG TỪ VỰNG -->
        <div id="tabWordBank" class="app-screen screen-hidden flex-1 flex flex-col p-4 overflow-hidden relative">
            <div class="space-y-2.5 flex-1 flex flex-col">
                <div class="flex items-center justify-between">
                    <div>
                        <h2 class="text-base font-black text-white">Ngân hàng từ vựng</h2>
                        <span class="text-[10px] text-slate-400">Song ngữ Anh - Việt &amp; IPA</span>
                    </div>
                    <button onclick="openAddWordModal()" class="press px-2.5 py-1.5 bg-indigo-600 hover:bg-indigo-500 text-white text-xs font-bold rounded-xl flex items-center gap-1 shadow">
                        <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v16m8-8H4"/></svg>
                        <span>Thêm từ</span>
                    </button>
                </div>

                <input type="text" id="wbSearchInput" oninput="filterWordBank()" placeholder="Tìm kiếm từ tiếng Anh hoặc nghĩa tiếng Việt..." class="w-full bg-slate-800 border border-slate-700 rounded-xl px-3 py-2 text-xs text-white focus:outline-none focus:border-indigo-500 transition-colors">

                <div class="flex items-center gap-1.5 text-[10px]">
                    <button onclick="setWordBankFilter('all')" id="wbFilterAll" class="press px-2.5 py-1 rounded-lg bg-indigo-600 text-white font-bold">Tất cả (<span id="wbTotalCount">8</span>)</button>
                    <button onclick="setWordBankFilter('learning')" id="wbFilterLearning" class="press px-2.5 py-1 rounded-lg bg-slate-800 text-slate-400 font-bold">Đang học</button>
                    <button onclick="setWordBankFilter('mastered')" id="wbFilterMastered" class="press px-2.5 py-1 rounded-lg bg-slate-800 text-slate-400 font-bold">Đã thuộc</button>
                </div>

                <div id="wordBankListContainer" class="flex-1 overflow-y-auto thin-scroll space-y-2 pr-1"></div>
            </div>
        </div>

        <!-- 7. QUÉT SÁCH -->
        <div id="tabScan" class="app-screen screen-hidden flex-1 flex flex-col relative bg-slate-900 overflow-hidden">
            <div class="p-3 bg-slate-950 flex items-center justify-between border-b border-slate-800 z-10">
      <span class="text-xs font-bold text-white flex items-center gap-1.5">
        <span class="w-2 h-2 rounded-full bg-indigo-500"></span> Nạp Từ Mới
      </span>
                <div class="flex items-center gap-1 text-[10px]">
                    <button id="scanModeAutoBtn" onclick="setScanModeUI('auto')" class="press px-2.5 py-1 rounded-md bg-indigo-600 text-white font-bold">Tự động</button>
                    <button id="scanModeSemiBtn" onclick="setScanModeUI('semi')" class="press px-2.5 py-1 rounded-md bg-slate-800 text-slate-400 font-bold">Bút gạch</button>
                </div>
            </div>

            <div class="flex-1 p-3 relative flex items-center justify-center">
                <div class="w-full h-full bg-amber-50 rounded-2xl p-4 relative overflow-hidden flex flex-col justify-center border-2 border-dashed border-indigo-400/80 shadow-inner">
                    <div id="scanLaser" class="scanner-line hidden"></div>
                    <div class="text-[12px] leading-relaxed text-slate-800 font-serif italic text-justify select-none overflow-y-auto max-h-[360px] thin-scroll" id="bookPagePreview">
                        The human mind exhibits a resilient cognitive architecture. While momentary thoughts are ephemeral, deep memories undergo meticulous consolidation. This profound process allows organisms to navigate ambiguous environments and formulate plausible hypotheses.
                    </div>
                    <div class="absolute top-2 left-1/2 -translate-x-1/2 bg-slate-900/85 backdrop-blur px-3 py-1 rounded-full text-[10px] text-indigo-300 font-semibold">
                        Đặt trang sách vào khung
                    </div>
                </div>
            </div>

            <div class="p-3 bg-slate-950 flex items-center justify-around z-10">
                <button id="scanCaptureBtn" onclick="handleScanCapture()" class="press w-13 h-13 p-3 rounded-full bg-indigo-600 border-4 border-white flex items-center justify-center shadow-lg hover:bg-indigo-500">
                    <svg class="w-6 h-6 text-white" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 9a2 2 0 012-2h.93a2 2 0 001.664-.89l.812-1.22A2 2 0 0110.07 4h3.86a2 2 0 011.664.89l.812 1.22A2 2 0 0018.07 7H19a2 2 0 012 2v9a2 2 0 01-2 2H5a2 2 0 01-2-2V9z"></path></svg>
                </button>
            </div>
        </div>

        <!-- MODAL CRUD -->
        <div id="crudModal" class="hidden absolute inset-0 bg-slate-950/95 backdrop-blur-sm z-50 flex-col p-4 justify-between">
            <div class="modal-in space-y-3 overflow-y-auto thin-scroll">
                <div class="flex items-center justify-between border-b border-slate-800 pb-2">
                    <h3 id="crudModalTitle" class="text-sm font-bold text-white">Thêm từ vựng mới</h3>
                    <button onclick="closeCrudModal()" class="press text-slate-400 hover:text-white text-xs">✕ Đóng</button>
                </div>

                <div class="space-y-1">
                    <label class="text-[10px] font-bold text-slate-300 uppercase">Từ tiếng Anh *</label>
                    <div class="flex gap-2">
                        <input type="text" id="crudWordInput" oninput="autoLookupDict(this.value)" placeholder="Ví dụ: ephemeral" class="flex-1 bg-slate-800 border border-slate-700 rounded-lg px-2.5 py-1.5 text-xs text-white focus:outline-none focus:border-indigo-500 transition-colors">
                        <button onclick="triggerLookup()" class="press px-2.5 py-1 bg-indigo-600 text-white rounded-lg text-[10px] font-bold">Tra mẫu</button>
                    </div>
                </div>

                <div class="space-y-1">
                    <label class="text-[10px] font-bold text-slate-300 uppercase">Phiên âm IPA</label>
                    <input type="text" id="crudIPAInput" placeholder="/ɪˈfem.ər.əl/" class="w-full bg-slate-800 border border-slate-700 rounded-lg px-2.5 py-1.5 text-xs text-white focus:outline-none focus:border-indigo-500 transition-colors">
                </div>

                <div class="space-y-1">
                    <label class="text-[10px] font-bold text-slate-300 uppercase">Nghĩa Tiếng Việt *</label>
                    <input type="text" id="crudVietnameseInput" placeholder="Ví dụ: phù du, ngắn ngủi" class="w-full bg-slate-800 border border-slate-700 rounded-lg px-2.5 py-1.5 text-xs text-white focus:outline-none focus:border-indigo-500 transition-colors">
                </div>

                <div class="space-y-1">
                    <label class="text-[10px] font-bold text-slate-300 uppercase">Định nghĩa Anh - Anh</label>
                    <input type="text" id="crudDefInput" placeholder="lasting for a very short time" class="w-full bg-slate-800 border border-slate-700 rounded-lg px-2.5 py-1.5 text-xs text-white focus:outline-none focus:border-indigo-500 transition-colors">
                </div>

                <div class="space-y-1">
                    <label class="text-[10px] font-bold text-slate-300 uppercase">Câu ví dụ mẫu</label>
                    <input type="text" id="crudExampleInput" placeholder="Momentary thoughts are ephemeral." class="w-full bg-slate-800 border border-slate-700 rounded-lg px-2.5 py-1.5 text-xs text-white focus:outline-none focus:border-indigo-500 transition-colors">
                </div>
            </div>

            <div class="pt-3 border-t border-slate-800 flex items-center justify-end gap-2">
                <button onclick="closeCrudModal()" class="press px-3 py-1.5 bg-slate-800 text-slate-300 text-xs rounded-xl">Hủy</button>
                <button onclick="saveCrudWord()" class="press px-4 py-1.5 bg-indigo-600 text-white text-xs font-bold rounded-xl shadow">Lưu từ vựng</button>
            </div>
        </div>

        <!-- MODAL CONFIRM (thay cho confirm() gốc) -->
        <div id="confirmModal" class="hidden absolute inset-0 bg-slate-950/90 backdrop-blur-sm z-50 items-center justify-center p-6">
            <div class="modal-in w-full bg-slate-900 border border-slate-700 rounded-2xl p-4 space-y-4 shadow-2xl">
                <p id="confirmModalText" class="text-xs text-slate-200 leading-relaxed text-center"></p>
                <div class="grid grid-cols-2 gap-2">
                    <button onclick="closeConfirmModal(false)" class="press py-2 rounded-xl bg-slate-800 text-slate-300 text-xs font-bold">Hủy</button>
                    <button onclick="closeConfirmModal(true)" class="press py-2 rounded-xl bg-rose-600 hover:bg-rose-500 text-white text-xs font-bold">Xác nhận</button>
                </div>
            </div>
        </div>

        <!-- TOAST -->
        <div id="toastHost" class="absolute left-1/2 bottom-20 z-[60] w-[86%] pointer-events-none"></div>

    </div>

    <!-- BOTTOM NAVIGATION -->
    <div id="bottomNavBar" class="bg-slate-950 border-t border-slate-800 flex items-center justify-around z-30" style="padding-bottom: env(safe-area-inset-bottom, 0px); min-height: calc(3.5rem + env(safe-area-inset-bottom, 0px));">
        <button onclick="switchAppTab('home')" id="navHome" class="press flex flex-col items-center text-indigo-400 text-[10px] font-bold">
            <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 20 20"><path d="M10.707 2.293a1 1 0 00-1.414 0l-7 7a1 1 0 001.414 1.414L4 10.414V17a1 1 0 001 1h2a1 1 0 001-1v-2a1 1 0 011-1h2a1 1 0 011 1v2a1 1 0 001 1h2a1 1 0 001-1v-6.586l.293.293a1 1 0 001.414-1.414l-7-7z"></path></svg>
            <span>Học tập</span>
        </button>

        <button onclick="switchAppTab('flashcards')" id="navFlashcards" class="press flex flex-col items-center text-slate-400 hover:text-slate-200 text-[10px] font-medium">
            <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 11H5m14 0a2 2 0 012 2v6a2 2 0 01-2 2H5a2 2 0 01-2-2v-6a2 2 0 012-2m14 0V9a2 2 0 00-2-2M5 11V9a2 2 0 012-2m0 0V5a2 2 0 012-2h6a2 2 0 012 2v2M7 7h10"></path></svg>
            <span>Flashcard</span>
        </button>

        <button onclick="switchAppTab('wordbank')" id="navWordBank" class="press flex flex-col items-center text-slate-400 hover:text-slate-200 text-[10px] font-medium">
            <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 6.253v13m0-13C10.832 5.477 9.246 5 7.5 5S4.168 5.477 3 6.253v13C4.168 18.477 5.754 18 7.5 18s3.332.477 4.5 1.253m0-13C13.168 5.477 14.754 5 16.5 5c1.747 0 3.332.477 4.5 1.253v13C19.832 18.477 18.247 18 16.5 18c-1.746 0-3.332.477-4.5 1.253"></path></svg>
            <span>Kho từ</span>
        </button>

        <button onclick="switchAppTab('scan')" id="navScan" class="press flex flex-col items-center text-slate-400 hover:text-slate-200 text-[10px] font-medium">
            <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 9a2 2 0 012-2h.93a2 2 0 001.664-.89l.812-1.22A2 2 0 0110.07 4h3.86a2 2 0 011.664.89l.812 1.22A2 2 0 0018.07 7H19a2 2 0 012 2v9a2 2 0 01-2 2H5a2 2 0 01-2-2V9z"></path></svg>
            <span>Quét sách</span>
        </button>
    </div>

</div>

<script>
    const offlineDict = {
      "ephemeral": { phonetic: "/ɪˈfem.ər.əl/", vi: "phù du, ngắn ngủi, sớm nở tối tàn", def: "lasting for a very short time; fleeting", ex: "Momentary thoughts are ephemeral." },
      "meticulous": { phonetic: "/məˈtɪk.jə.ləs/", vi: "tỉ mỉ, cẩn thận, kỹ lưỡng", def: "showing great attention to detail; very precise", ex: "He was meticulous about his laboratory notes." },
      "ambiguous": { phonetic: "/æmˈbɪɡ.ju.əs/", vi: "mơ hồ, đa nghĩa, không rõ ràng", def: "open to more than one interpretation", ex: "The election results were ambiguous." },
      "ubiquitous": { phonetic: "/juːˈbɪk.wɪ.təs/", vi: "phổ biến khắp nơi, có mặt ở mọi nơi", def: "present, appearing, or found everywhere", ex: "Smartphones are ubiquitous in modern society." },
      "resilient": { phonetic: "/rɪˈzɪl.jənt/", vi: "kiên cường, phục hồi nhanh", def: "able to withstand or recover quickly", ex: "She has a resilient personality." },
      "plausible": { phonetic: "/ˈplɔː.zə.bəl/", vi: "hợp lý, đáng tin cậy, có vẻ đúng", def: "seeming reasonable or probable; believable", ex: "The scientist proposed a plausible hypothesis." },
      "profound": { phonetic: "/prəˈfaʊnd/", vi: "sâu sắc, uyên thâm, to lớn", def: "very great or intense; showing great insight", ex: "The speech had a profound impact." },
      "benevolent": { phonetic: "/bəˈnev.əl.ənt/", vi: "nhân từ, từ thiện, hào hiệp", def: "well meaning and kindly; charitable", ex: "A benevolent donor provided funds." }
    };

    let vocabularyBank = [
      { id: '1', word: 'ephemeral', phonetic: '/ɪˈfem.ər.əl/', vi: 'phù du, ngắn ngủi, sớm nở tối tàn', def: 'lasting for a very short time; fleeting', example: 'Momentary thoughts are ephemeral.', status: 'learning', source: 'Sách Tâm lý' },
      { id: '2', word: 'meticulous', phonetic: '/məˈtɪk.jə.ləs/', vi: 'tỉ mỉ, cẩn thận, kỹ lưỡng', def: 'showing great attention to detail; very precise', example: 'He was meticulous about his laboratory notes.', status: 'mastered', source: 'Sách Khoa học' },
      { id: '3', word: 'ambiguous', phonetic: '/æmˈbɪɡ.ju.əs/', vi: 'mơ hồ, đa nghĩa, không rõ ràng', def: 'open to more than one interpretation', example: 'The election results were ambiguous.', status: 'learning', source: 'Sách Xã hội' },
      { id: '4', word: 'ubiquitous', phonetic: '/juːˈbɪk.wɪ.təs/', vi: 'phổ biến khắp nơi, có mặt ở mọi nơi', def: 'present, appearing, or found everywhere', example: 'Smartphones are ubiquitous in modern life.', status: 'learning', source: 'Sách Công nghệ' },
      { id: '5', word: 'resilient', phonetic: '/rɪˈzɪl.jənt/', vi: 'kiên cường, phục hồi nhanh', def: 'able to withstand or recover quickly', example: 'She has a resilient personality.', status: 'mastered', source: 'Sách Phát triển bản thân' },
      { id: '6', word: 'plausible', phonetic: '/ˈplɔː.zə.bəl/', vi: 'hợp lý, đáng tin cậy', def: 'seeming reasonable or probable', example: 'The scientist proposed a plausible hypothesis.', status: 'learning', source: 'Sách Nghiên cứu' },
      { id: '7', word: 'profound', phonetic: '/prəˈfaʊnd/', vi: 'sâu sắc, uyên thâm, to lớn', def: 'very great or intense; showing great insight', example: 'The speech had a profound impact.', status: 'mastered', source: 'Sách Triết học' },
      { id: '8', word: 'benevolent', phonetic: '/bəˈnev.əl.ənt/', vi: 'nhân từ, từ thiện, hào hiệp', def: 'well meaning and kindly; charitable', example: 'A benevolent donor provided books.', status: 'learning', source: 'Sách Lịch sử' }
    ];

    let editingCardId = null;
    let currentScanMode = 'auto';
    let flashcardIndex = 0;
    let isFlipped = false;
    let currentWbFilter = 'all';
    let studiedToday = true; // becomes true as soon as today's goal has progress

    let quizList = [];
    let currentQuizIndex = 0;
    let quizScore = 0;
    let quizAnswered = false;

    let listeningList = [];
    let currentListeningIndex = 0;
    let listeningScore = 0;
    let listeningAnswered = false;

    const ALL_SCREEN_IDS = ['tabHome', 'tabFlashcards', 'tabScan', 'tabWordBank', 'screenQuizView', 'screenQuizSummary', 'screenListeningView'];

    // --- Smooth screen switching (fade + slight rise, single transition system) ---
    function showScreen(targetId) {
      const current = ALL_SCREEN_IDS.map(id => document.getElementById(id)).find(el => !el.classList.contains('screen-hidden'));
      const target = document.getElementById(targetId);

      if (current === target) return;

      if (current) {
        current.classList.add('screen-leaving');
        setTimeout(() => {
          current.classList.add('screen-hidden');
          current.classList.remove('screen-leaving');
        }, 140);
      }

      target.classList.remove('screen-hidden');
      target.classList.add('screen-entering');
      target.classList.remove('hidden');
      // force reflow so the entering state applies before transitioning out
      void target.offsetWidth;
      requestAnimationFrame(() => target.classList.remove('screen-entering'));
    }

    function switchAppTab(tabId) {
      const navIds = ['navHome', 'navFlashcards', 'navScan', 'navWordBank'];
      navIds.forEach(id => {
        document.getElementById(id).className = "press flex flex-col items-center text-slate-400 hover:text-slate-200 text-[10px] font-medium";
      });

      if (tabId === 'home') {
        showScreen('tabHome');
        document.getElementById('navHome').className = "press flex flex-col items-center text-indigo-400 text-[10px] font-bold";
        renderHomePreview();
      } else if (tabId === 'flashcards') {
        showScreen('tabFlashcards');
        document.getElementById('navFlashcards').className = "press flex flex-col items-center text-indigo-400 text-[10px] font-bold";
        renderFlashcard();
      } else if (tabId === 'scan') {
        showScreen('tabScan');
        document.getElementById('navScan').className = "press flex flex-col items-center text-indigo-400 text-[10px] font-bold";
      } else if (tabId === 'wordbank') {
        showScreen('tabWordBank');
        document.getElementById('navWordBank').className = "press flex flex-col items-center text-indigo-400 text-[10px] font-bold";
        renderWordBank();
      }
    }

    // --- Toast (thay cho alert()) ---
    function showToast(message, tone = 'info') {
      const host = document.getElementById('toastHost');
      const toneStyles = {
        info: 'bg-slate-800 border-slate-700 text-slate-100',
        success: 'bg-emerald-600/95 border-emerald-400/40 text-white',
        warn: 'bg-amber-600/95 border-amber-400/40 text-white'
      };
      const el = document.createElement('div');
      el.className = `toast-in pointer-events-auto border ${toneStyles[tone] || toneStyles.info} rounded-xl px-4 py-2.5 text-[11px] font-semibold shadow-2xl text-center absolute left-1/2 bottom-0`;
      el.style.transform = 'translateX(-50%)';
      el.innerText = message;
      host.innerHTML = '';
      host.appendChild(el);
      setTimeout(() => {
        el.classList.remove('toast-in');
        el.classList.add('toast-out');
        setTimeout(() => el.remove(), 240);
      }, 2200);
    }

    // --- Custom confirm (thay cho confirm()) ---
    let pendingConfirmCallback = null;
    function askConfirm(message, onYes) {
      document.getElementById('confirmModalText').innerText = message;
      document.getElementById('confirmModal').classList.remove('hidden');
      document.getElementById('confirmModal').classList.add('flex');
      pendingConfirmCallback = onYes;
    }
    function closeConfirmModal(confirmed) {
      document.getElementById('confirmModal').classList.add('hidden');
      document.getElementById('confirmModal').classList.remove('flex');
      if (confirmed && typeof pendingConfirmCallback === 'function') pendingConfirmCallback();
      pendingConfirmCallback = null;
    }

    // --- QUIZ ---
    function startQuizMode() {
      // Fix: cần tối thiểu 2 từ để có ít nhất 1 đáp án gây nhiễu, tránh câu hỏi chỉ có 1 lựa chọn
      if (vocabularyBank.length < 2) {
        showToast("Cần tối thiểu 2 từ vựng để làm Quiz!", 'warn');
        return;
      }

      quizList = vocabularyBank.map(card => {
        const correctAnswer = card.vi || card.def;
        const otherOptions = vocabularyBank
          .filter(x => x.word !== card.word)
          .map(x => x.vi || x.def)
          .sort(() => Math.random() - 0.5)
          .slice(0, 3);

        const options = [...otherOptions, correctAnswer].sort(() => Math.random() - 0.5);
        return {
          word: card.word,
          phonetic: card.phonetic,
          correct: correctAnswer,
          options: options,
          correctIndex: options.indexOf(correctAnswer)
        };
      });

      currentQuizIndex = 0;
      quizScore = 0;

      showScreen('screenQuizView');
      renderQuizQuestion();
    }

    function renderQuizQuestion() {
      quizAnswered = false;
      const q = quizList[currentQuizIndex];
      document.getElementById('quizViewCounter').innerText = `Câu ${currentQuizIndex + 1} / ${quizList.length}`;
      document.getElementById('quizViewScore').innerText = `${quizScore} Điểm`;
      document.getElementById('quizViewProgressBar').style.width = `${((currentQuizIndex + 1) / quizList.length) * 100}%`;

      document.getElementById('quizTargetWord').innerText = q.word;
      document.getElementById('quizTargetIPA').innerText = q.phonetic || '/.../';
      document.getElementById('btnQuizNext').classList.add('hidden');

      speakUK(q.word);

      const container = document.getElementById('quizOptionsContainer');
      container.innerHTML = '';

      q.options.forEach((opt, idx) => {
        const btn = document.createElement('button');
        btn.className = "option-rise press w-full text-left p-3 rounded-xl border border-slate-700 bg-slate-800/90 text-slate-200 text-xs hover:border-indigo-500/50 hover:bg-slate-700/60 flex items-start gap-2.5";
        btn.style.animationDelay = `${idx * 55}ms`;
        btn.innerHTML = `
          <span class="font-bold text-indigo-400">${String.fromCharCode(65 + idx)}.</span>
          <span class="flex-1 text-[11px] leading-tight font-medium">${opt}</span>
        `;
        btn.onclick = () => selectQuizOption(idx);
        container.appendChild(btn);
      });
    }

    function selectQuizOption(idx) {
      if (quizAnswered) return;
      quizAnswered = true;
      const q = quizList[currentQuizIndex];
      const buttons = document.getElementById('quizOptionsContainer').children;

      if (idx === q.correctIndex) {
        quizScore++;
        buttons[idx].className = "press w-full text-left p-3 rounded-xl border-2 border-emerald-500 bg-emerald-500/20 text-emerald-200 text-xs flex items-start gap-2.5";
      } else {
        buttons[idx].className = "press w-full text-left p-3 rounded-xl border-2 border-rose-500 bg-rose-500/20 text-rose-200 text-xs flex items-start gap-2.5";
        buttons[q.correctIndex].className = "press w-full text-left p-3 rounded-xl border-2 border-emerald-500 bg-emerald-500/20 text-emerald-200 text-xs flex items-start gap-2.5";
      }

      document.getElementById('quizViewScore').innerText = `${quizScore} Điểm`;
      document.getElementById('btnQuizNext').classList.remove('hidden');
      document.getElementById('btnQuizNext').innerText = (currentQuizIndex + 1 < quizList.length) ? "Câu tiếp theo →" : "Xem tổng kết";
    }

    function nextQuizQuestion() {
      if (currentQuizIndex + 1 < quizList.length) {
        currentQuizIndex++;
        renderQuizQuestion();
      } else {
        showQuizSummary();
      }
    }

    function launchConfetti() {
      const layer = document.getElementById('confettiLayer');
      layer.innerHTML = '';
      const colors = ['#6366f1', '#a855f7', '#10b981', '#fbbf24'];
      for (let i = 0; i < 24; i++) {
        const piece = document.createElement('span');
        piece.className = 'confetti-piece';
        piece.style.left = `${Math.random() * 100}%`;
        piece.style.width = `${4 + Math.random() * 4}px`;
        piece.style.height = `${8 + Math.random() * 6}px`;
        piece.style.background = colors[i % colors.length];
        piece.style.animationDelay = `${Math.random() * 0.3}s`;
        layer.appendChild(piece);
      }
      setTimeout(() => { layer.innerHTML = ''; }, 2000);
    }

    function showQuizSummary() {
      showScreen('screenQuizSummary');

      const pct = Math.round((quizScore / quizList.length) * 100);
      document.getElementById('quizSummaryScoreText').innerText = `Bạn đã trả lời đúng ${quizScore}/${quizList.length} câu (${pct}%)`;

      const listContainer = document.getElementById('quizSummaryList');
      listContainer.innerHTML = '';
      quizList.forEach(q => {
        const item = document.createElement('div');
        item.className = "p-2 bg-slate-900/80 rounded-lg border border-slate-800 flex items-center justify-between gap-2";
        item.innerHTML = `
          <div>
            <div class="flex items-center gap-1.5">
              <b class="text-indigo-400 uppercase text-xs">${q.word}</b>
              <span class="text-[10px] text-slate-400 font-mono">${q.phonetic || ''}</span>
            </div>
            <div class="text-slate-300 text-[11px] font-semibold">${q.correct}</div>
          </div>
          <button onclick="speakUK('${q.word}')" class="press p-1.5 bg-slate-800 hover:bg-slate-700 text-indigo-300 rounded-full flex-shrink-0" title="Phát âm UK">
            <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15.536 8.464a5 5 0 010 7.072m2.828-9.9a9 9 0 010 12.728M5.586 15H4a1 1 0 01-1-1v-4a1 1 0 011-1h1.586l4.707-4.707C10.923 3.663 12 4.109 12 5v14c0 .891-1.077 1.337-1.707.707L5.586 15z"></path></svg>
          </button>
        `;
        listContainer.appendChild(item);
      });

      if (pct >= 60) setTimeout(launchConfetti, 180);
    }

    // --- LISTENING ---
    function startListeningMode() {
      if (vocabularyBank.length < 4) {
        showToast("Cần tối thiểu 4 từ vựng để tạo bài luyện nghe!", 'warn');
        return;
      }

      listeningList = vocabularyBank.slice().sort(() => Math.random() - 0.5).slice(0, 5).map(card => {
        const others = vocabularyBank.filter(x => x.word !== card.word).map(x => x.word).sort(() => Math.random() - 0.5).slice(0, 3);
        const options = [...others, card.word].sort(() => Math.random() - 0.5);
        return { word: card.word, options: options };
      });

      currentListeningIndex = 0;
      listeningScore = 0;

      showScreen('screenListeningView');
      renderListeningQuestion();
    }

    function renderListeningQuestion() {
      listeningAnswered = false;
      const q = listeningList[currentListeningIndex];
      document.getElementById('listeningScoreBadge').innerText = `${listeningScore} Điểm`;
      document.getElementById('btnListeningNext').classList.add('hidden');

      playCurrentListeningWord();

      const container = document.getElementById('listeningOptionsContainer');
      container.innerHTML = '';

      q.options.forEach((opt, idx) => {
        const btn = document.createElement('button');
        btn.className = "option-rise press w-full text-center p-3 rounded-xl border border-slate-700 bg-slate-800 text-white font-bold text-xs hover:border-purple-500 uppercase";
        btn.style.animationDelay = `${idx * 55}ms`;
        btn.innerText = opt;
        btn.onclick = () => selectListeningOption(opt, btn);
        container.appendChild(btn);
      });
    }

    function playCurrentListeningWord() {
      const q = listeningList[currentListeningIndex];
      if (q) speakUK(q.word);
    }

    function selectListeningOption(selectedWord, btn) {
      if (listeningAnswered) return;
      listeningAnswered = true;
      const q = listeningList[currentListeningIndex];

      if (selectedWord === q.word) {
        listeningScore++;
        btn.className = "press w-full text-center p-3 rounded-xl border-2 border-emerald-500 bg-emerald-500/20 text-emerald-300 font-bold text-xs uppercase";
      } else {
        btn.className = "press w-full text-center p-3 rounded-xl border-2 border-rose-500 bg-rose-500/20 text-rose-300 font-bold text-xs uppercase";
      }

      document.getElementById('listeningScoreBadge').innerText = `${listeningScore} Điểm`;
      document.getElementById('btnListeningNext').classList.remove('hidden');
      document.getElementById('btnListeningNext').innerText = (currentListeningIndex + 1 < listeningList.length) ? "Câu tiếp theo →" : "Xong bài nghe";
    }

    function nextListeningQuestion() {
      if (currentListeningIndex + 1 < listeningList.length) {
        currentListeningIndex++;
        renderListeningQuestion();
      } else {
        showToast(`Chúc mừng! Bạn đạt ${listeningScore}/${listeningList.length} điểm bài luyện nghe!`, 'success');
        switchAppTab('home');
      }
    }

    // --- HOME PREVIEW ---
    function renderHomePreview() {
      document.getElementById('homeVocabCount').innerText = `${vocabularyBank.length} từ đã lưu`;
      const container = document.getElementById('homeWordPreviewList');
      container.innerHTML = '';

      vocabularyBank.slice(0, 3).forEach(card => {
        const div = document.createElement('div');
        div.className = "p-2.5 bg-slate-800/80 rounded-xl border border-slate-700/80 flex items-center justify-between";
        div.innerHTML = `
          <div>
            <div class="flex items-center gap-1.5">
              <b class="text-indigo-400 uppercase text-xs">${card.word}</b>
              <span class="text-[10px] text-slate-400 font-mono">${card.phonetic}</span>
            </div>
            <div class="text-[11px] font-semibold text-slate-200 line-clamp-1 mt-0.5">${card.vi}</div>
          </div>
          <button onclick="speakUK('${card.word}')" class="press p-1.5 bg-slate-700 hover:bg-slate-600 text-indigo-300 rounded-full flex-shrink-0">
            <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15.536 8.464a5 5 0 010 7.072m2.828-9.9a9 9 0 010 12.728M5.586 15H4a1 1 0 01-1-1v-4a1 1 0 011-1h1.586l4.707-4.707C10.923 3.663 12 4.109 12 5v14c0 .891-1.077 1.337-1.707.707L5.586 15z"></path></svg>
          </button>
        `;
        container.appendChild(div);
      });
    }

    // --- FLASHCARDS ---
    function renderFlashcard() {
      if (vocabularyBank.length === 0) return;
      isFlipped = false;
      document.getElementById('flashcardInner').classList.remove('rotate-y-180');

      const card = vocabularyBank[flashcardIndex];
      document.getElementById('fcCounter').innerText = `Thẻ ${flashcardIndex + 1} / ${vocabularyBank.length}`;
      document.getElementById('fcProgressBar').style.width = `${((flashcardIndex + 1) / vocabularyBank.length) * 100}%`;
      document.getElementById('fcFrontWord').innerText = card.word;
      document.getElementById('fcFrontIPA').innerText = card.phonetic || '/.../';
      document.getElementById('fcBackVietnamese').innerText = card.vi || 'Chưa có nghĩa tiếng Việt';
      document.getElementById('fcBackDef').innerText = card.def;
      document.getElementById('fcBackExample').innerText = card.example || 'Example sentence from book.';

      speakUK(card.word);
    }

    function flipFlashcard() {
      isFlipped = !isFlipped;
      document.getElementById('flashcardInner').classList.toggle('rotate-y-180', isFlipped);
    }

    function handleFlashcardAction(mastered) {
      // Fix: tránh lỗi "Cannot read properties of undefined" khi vocabularyBank rỗng
      // hoặc flashcardIndex đã lệch khỏi độ dài mảng (vd sau khi xóa từ)
      if (vocabularyBank.length === 0) return;
      if (flashcardIndex >= vocabularyBank.length) flashcardIndex = 0;

      const card = vocabularyBank[flashcardIndex];
      card.status = mastered ? 'mastered' : 'learning';
      flashcardIndex = (flashcardIndex + 1) % vocabularyBank.length;
      renderFlashcard();
    }

    // --- WORD BANK CRUD ---
    function renderWordBank() {
      document.getElementById('wbTotalCount').innerText = vocabularyBank.length;
      filterWordBank();
    }

    function setWordBankFilter(f) {
      currentWbFilter = f;
      document.getElementById('wbFilterAll').className = "press " + (f === 'all' ? "px-2.5 py-1 rounded-lg bg-indigo-600 text-white font-bold" : "px-2.5 py-1 rounded-lg bg-slate-800 text-slate-400 font-bold");
      document.getElementById('wbFilterLearning').className = "press " + (f === 'learning' ? "px-2.5 py-1 rounded-lg bg-indigo-600 text-white font-bold" : "px-2.5 py-1 rounded-lg bg-slate-800 text-slate-400 font-bold");
      document.getElementById('wbFilterMastered').className = "press " + (f === 'mastered' ? "px-2.5 py-1 rounded-lg bg-indigo-600 text-white font-bold" : "px-2.5 py-1 rounded-lg bg-slate-800 text-slate-400 font-bold");
      filterWordBank();
    }

    function filterWordBank() {
      const q = document.getElementById('wbSearchInput').value.toLowerCase().trim();
      const container = document.getElementById('wordBankListContainer');
      container.innerHTML = '';

      const filtered = vocabularyBank.filter(item => {
        const matchQ = item.word.toLowerCase().includes(q) || (item.vi && item.vi.toLowerCase().includes(q)) || item.def.toLowerCase().includes(q);
        const matchF = currentWbFilter === 'all' || item.status === currentWbFilter;
        return matchQ && matchF;
      });

      if (filtered.length === 0) {
        container.innerHTML = '<div class="text-center text-slate-500 text-xs py-8">Không tìm thấy từ nào phù hợp.</div>';
        return;
      }

      filtered.forEach(card => {
        const div = document.createElement('div');
        div.className = "p-3 bg-slate-800/85 rounded-xl border border-slate-700/80 space-y-1.5";
        div.innerHTML = `
          <div class="flex items-center justify-between">
            <div class="flex items-center gap-2">
              <b class="text-indigo-400 uppercase text-xs tracking-wide">${card.word}</b>
              <span class="text-[10px] text-slate-400 font-mono">${card.phonetic || ''}</span>
              <button onclick="speakUK('${card.word}')" class="press text-slate-400 hover:text-indigo-300">
                <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15.536 8.464a5 5 0 010 7.072m2.828-9.9a9 9 0 010 12.728M5.586 15H4a1 1 0 01-1-1v-4a1 1 0 011-1h1.586l4.707-4.707C10.923 3.663 12 4.109 12 5v14c0 .891-1.077 1.337-1.707.707L5.586 15z"></path></svg>
              </button>
            </div>
            <span onclick="toggleWordStatus('${card.id}')" class="press text-[9px] px-2 py-0.5 rounded cursor-pointer ${card.status === 'mastered' ? 'bg-emerald-500/20 text-emerald-300' : 'bg-amber-500/20 text-amber-300'}">
              ${card.status === 'mastered' ? 'Đã thuộc' : 'Đang học'}
            </span>
          </div>

          <div class="text-xs font-bold text-white">${card.vi || 'Chưa có nghĩa tiếng Việt'}</div>
          <div class="text-[11px] text-slate-300 italic">${card.def}</div>

          <div class="flex items-center justify-between pt-1 border-t border-slate-700/50 text-[10px]">
            <span class="text-slate-400">Nguồn: ${card.source}</span>
            <div class="flex items-center gap-2">
              <button onclick="openEditWordModal('${card.id}')" class="press text-indigo-300 hover:text-indigo-200 font-semibold flex items-center gap-1">
                <svg class="w-3 h-3" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z"/></svg>
                Sửa
              </button>
              <button onclick="deleteWord('${card.id}')" class="press text-rose-400 hover:text-rose-300 font-semibold flex items-center gap-1">
                <svg class="w-3 h-3" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"/></svg>
                Xóa
              </button>
            </div>
          </div>
        `;
        container.appendChild(div);
      });
    }

    function toggleWordStatus(id) {
      const card = vocabularyBank.find(x => x.id === id);
      if (card) {
        card.status = card.status === 'mastered' ? 'learning' : 'mastered';
        renderWordBank();
      }
    }

    // --- CRUD MODAL ---
    function openAddWordModal() {
      editingCardId = null;
      document.getElementById('crudModalTitle').innerText = "Thêm từ vựng mới";
      document.getElementById('crudWordInput').value = '';
      document.getElementById('crudIPAInput').value = '';
      document.getElementById('crudVietnameseInput').value = '';
      document.getElementById('crudDefInput').value = '';
      document.getElementById('crudExampleInput').value = '';
      document.getElementById('crudModal').classList.remove('hidden');
      document.getElementById('crudModal').classList.add('flex');
    }

    function openEditWordModal(id) {
      editingCardId = id;
      const card = vocabularyBank.find(x => x.id === id);
      if (!card) return;

      document.getElementById('crudModalTitle').innerText = "Chỉnh sửa từ vựng";
      document.getElementById('crudWordInput').value = card.word;
      document.getElementById('crudIPAInput').value = card.phonetic || '';
      document.getElementById('crudVietnameseInput').value = card.vi || '';
      document.getElementById('crudDefInput').value = card.def || '';
      document.getElementById('crudExampleInput').value = card.example || '';
      document.getElementById('crudModal').classList.remove('hidden');
      document.getElementById('crudModal').classList.add('flex');
    }

    function closeCrudModal() {
      document.getElementById('crudModal').classList.add('hidden');
      document.getElementById('crudModal').classList.remove('flex');
    }

    function autoLookupDict(val) {
      const w = val.toLowerCase().trim();
      if (offlineDict[w]) {
        const d = offlineDict[w];
        if (!document.getElementById('crudIPAInput').value) document.getElementById('crudIPAInput').value = d.phonetic;
        if (!document.getElementById('crudVietnameseInput').value) document.getElementById('crudVietnameseInput').value = d.vi;
        if (!document.getElementById('crudDefInput').value) document.getElementById('crudDefInput').value = d.def;
        if (!document.getElementById('crudExampleInput').value) document.getElementById('crudExampleInput').value = d.ex;
      }
    }

    function triggerLookup() {
      const w = document.getElementById('crudWordInput').value.toLowerCase().trim();
      autoLookupDict(w);
    }

    function saveCrudWord() {
      const word = document.getElementById('crudWordInput').value.toLowerCase().trim();
      const phonetic = document.getElementById('crudIPAInput').value.trim();
      const vi = document.getElementById('crudVietnameseInput').value.trim();
      const def = document.getElementById('crudDefInput').value.trim();
      const example = document.getElementById('crudExampleInput').value.trim();

      if (!word) {
        showToast("Vui lòng nhập từ tiếng Anh!", 'warn');
        return;
      }

      // Fix: chặn trùng từ vựng (không phân biệt hoa/thường) trước khi lưu,
      // trừ trường hợp đang sửa chính từ đó
      const duplicate = vocabularyBank.find(x => x.word.toLowerCase() === word && x.id !== editingCardId);
      if (duplicate) {
        showToast(`Từ "${word}" đã có trong ngân hàng từ vựng!`, 'warn');
        return;
      }

      if (editingCardId) {
        const card = vocabularyBank.find(x => x.id === editingCardId);
        if (card) {
          card.word = word;
          card.phonetic = phonetic;
          card.vi = vi;
          card.def = def;
          card.example = example;
        }
        showToast("Đã cập nhật từ vựng!", 'success');
      } else {
        vocabularyBank.unshift({
          id: String(Date.now()),
          word: word,
          phonetic: phonetic,
          vi: vi || 'Chưa cập nhật nghĩa',
          def: def || 'Chưa cập nhật định nghĩa',
          example: example,
          status: 'learning',
          source: 'Thêm thủ công'
        });
        showToast("Đã thêm từ vựng mới!", 'success');
      }

      closeCrudModal();
      renderWordBank();
      renderHomePreview();
    }

    function deleteWord(id) {
      const card = vocabularyBank.find(x => x.id === id);
      askConfirm(`Xóa "${card ? card.word : 'từ này'}" khỏi ngân hàng từ vựng?`, () => {
        vocabularyBank = vocabularyBank.filter(x => x.id !== id);
        // Fix: nếu xóa từ khiến flashcardIndex vượt quá độ dài mảng mới, kéo về vị trí hợp lệ
        // (trước đây lỗi này làm app "đứng" khi bấm Đã thuộc/Chưa nhớ ở thẻ Flashcard sau khi xóa từ)
        if (flashcardIndex >= vocabularyBank.length) {
          flashcardIndex = 0;
        }
        renderWordBank();
        renderHomePreview();
        showToast("Đã xóa từ vựng.", 'info');
      });
    }

    // --- SCANNER ---
    function setScanModeUI(mode) {
      currentScanMode = mode;
      document.getElementById('scanModeAutoBtn').className = "press " + (mode === 'auto' ? "px-2.5 py-1 rounded-md bg-indigo-600 text-white font-bold" : "px-2.5 py-1 rounded-md bg-slate-800 text-slate-400 font-bold");
      document.getElementById('scanModeSemiBtn').className = "press " + (mode === 'semi' ? "px-2.5 py-1 rounded-md bg-indigo-600 text-white font-bold" : "px-2.5 py-1 rounded-md bg-slate-800 text-slate-400 font-bold");
    }

    async function handleScanCapture() {
      const btn = document.getElementById('scanCaptureBtn');
      btn.disabled = true;
      document.getElementById('scanLaser').classList.remove('hidden');
      await new Promise(r => setTimeout(r, 900));
      document.getElementById('scanLaser').classList.add('hidden');
      btn.disabled = false;
      showToast("Đã quét và nạp từ mới kèm Nghĩa Tiếng Việt & IPA!", 'success');
      switchAppTab('wordbank');
    }

    function speakUK(text) {
      if ('speechSynthesis' in window) {
        window.speechSynthesis.cancel();
        const u = new SpeechSynthesisUtterance(text);
        u.lang = 'en-GB';
        u.pitch = 1.0;
        u.rate = 0.92;
        const voices = window.speechSynthesis.getVoices();
        const uk = voices.find(v => v.lang.includes('en-GB') || v.name.includes('UK') || v.name.includes('British'));
        if (uk) u.voice = uk;
        window.speechSynthesis.speak(u);
      }
    }

    // --- Streak flame (purple + shake once today's study goal has progress) ---
    function renderStreak() {
      const badge = document.getElementById('streakBadge');
      const flame = document.getElementById('streakFlame');
      if (studiedToday) {
        badge.className = "flex items-center gap-1.5 px-3 py-1 rounded-full text-xs font-bold transition-colors bg-purple-500/10 border border-purple-500/30 text-purple-300";
        flame.className = "w-4 h-4 streak-flame is-active";
        flame.style.color = "#c084fc";
      } else {
        badge.className = "flex items-center gap-1.5 px-3 py-1 rounded-full text-xs font-bold transition-colors bg-slate-800 border border-slate-700 text-slate-400";
        flame.className = "w-4 h-4 streak-flame is-inactive";
        flame.style.color = "#94a3b8";
      }
    }

    // Khởi động tại Home Tab
    renderStreak();
    switchAppTab('home');
</script>
</body>
</html>
