<!DOCTYPE html>
<html lang="id" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Architecture - Task Manager & Reminder Bot</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    colors: {
                        brand: {
                            50: '#eff6ff',
                            100: '#dbeafe',
                            400: '#60a5fa',
                            500: '#3b82f6',
                            600: '#2563eb',
                            900: '#1e3a8a',
                        },
                        wa: '#25D366',
                        ai: '#a855f7',
                        db: '#336791',
                        cron: '#f59e0b'
                    },
                    animation: {
                        'pulse-slow': 'pulse 3s cubic-bezier(0.4, 0, 0.6, 1) infinite',
                        'flow-right': 'flowRight 2s linear infinite',
                    },
                    keyframes: {
                        flowRight: {
                            '0%': { transform: 'translateX(-100%)' },
                            '100%': { transform: 'translateX(100%)' },
                        }
                    }
                }
            }
        }
    </script>
    <!-- Google Fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&family=JetBrains+Mono:wght@400;500&display=swap" rel="stylesheet">
    <!-- Lucide Icons CDN -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        body {
            font-family: 'Plus Jakarta Sans', sans-serif;
        }
        code, pre {
            font-family: 'JetBrains Mono', monospace;
        }
        .glass-panel {
            background: rgba(30, 41, 59, 0.7);
            backdrop-filter: blur(12px);
            border: 1px solid rgba(255, 255, 255, 0.08);
        }
        .glass-card {
            background: rgba(15, 23, 42, 0.6);
            border: 1px solid rgba(255, 255, 255, 0.05);
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        }
        .glass-card:hover {
            border-color: rgba(59, 130, 246, 0.4);
            transform: translateY(-2px);
            box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.5), 0 8px 10px -6px rgba(59, 130, 246, 0.1);
        }
        .flow-line {
            position: relative;
            overflow: hidden;
        }
        .flow-line::after {
            content: '';
            position: absolute;
            top: 0; left: 0; right: 0; bottom: 0;
            background: linear-gradient(90deg, transparent, rgba(59, 130, 246, 0.8), transparent);
            animation: flowRight 2s linear infinite;
        }
    </style>
</head>
<body class="bg-slate-950 text-slate-100 min-h-screen p-4 sm:p-6 md:p-10 selection:bg-brand-500 selection:text-white">

    <div class="max-w-6xl mx-auto space-y-8">
        
        <!-- Header / Banner Repo -->
        <header class="glass-panel rounded-2xl p-6 md:p-8 relative overflow-hidden shadow-2xl">
            <!-- Background Glow Decor -->
            <div class="absolute -top-24 -right-24 w-96 h-96 bg-brand-500/10 rounded-full blur-3xl pointer-events-none"></div>
            <div class="absolute -bottom-24 -left-24 w-96 h-96 bg-purple-500/10 rounded-full blur-3xl pointer-events-none"></div>

            <div class="relative z-10 flex flex-col md:flex-row md:items-center justify-between gap-6">
                <div>
                    <div class="flex items-center gap-3 mb-3">
                        <span class="inline-flex items-center px-3 py-1 rounded-full text-xs font-semibold bg-emerald-500/10 text-emerald-400 border border-emerald-500/20">
                            <span class="w-2 h-2 rounded-full bg-emerald-400 animate-pulse mr-2"></span> Architecture Docs v1.0
                        </span>
                        <span class="text-xs text-slate-400 flex items-center gap-1">
                            <i data-lucide="git-branch" class="w-3.5 h-3.5"></i> main
                        </span>
                    </div>
                    <h1 class="text-3xl sm:text-4xl font-extrabold tracking-tight text-white mb-2 flex items-center gap-3">
                        <span>Task Manager & Reminder Bot</span>
                    </h1>
                    <p class="text-slate-400 text-base max-w-2xl">
                        Sistem manajemen tugas cerdas berbasis WhatsApp yang menggabungkan kemampuan <strong>Natural Language Processing (LLM)</strong> dan otomatisasi <strong>Scheduled Jobs</strong>.
                    </p>
                </div>

                <!-- Tech Stack Badges -->
                <div class="flex flex-wrap md:flex-col gap-2 shrink-0">
                    <div class="flex items-center gap-2 px-3 py-1.5 rounded-lg bg-slate-900/80 border border-slate-800 text-xs font-medium text-slate-300">
                        <i data-lucide="message-square" class="w-4 h-4 text-emerald-400"></i> whatsapp-web.js
                    </div>
                    <div class="flex items-center gap-2 px-3 py-1.5 rounded-lg bg-slate-900/80 border border-slate-800 text-xs font-medium text-slate-300">
                        <i data-lucide="sparkles" class="w-4 h-4 text-purple-400"></i> LLM API Engine
                    </div>
                    <div class="flex items-center gap-2 px-3 py-1.5 rounded-lg bg-slate-900/80 border border-slate-800 text-xs font-medium text-slate-300">
                        <i data-lucide="database" class="w-4 h-4 text-blue-400"></i> PostgreSQL
                    </div>
                    <div class="flex items-center gap-2 px-3 py-1.5 rounded-lg bg-slate-900/80 border border-slate-800 text-xs font-medium text-slate-300">
                        <i data-lucide="clock" class="w-4 h-4 text-amber-400"></i> Node-Cron Scheduler
                    </div>
                </div>
            </div>
        </header>

        <!-- BAGIAN 1: GRAFIK FLOW VISUAL (MODERN DASHBOARD BOARD) -->
        <section class="glass-panel rounded-2xl p-6 md:p-8 shadow-2xl relative">
            <div class="flex flex-col md:flex-row md:items-center justify-between gap-4 mb-8 pb-4 border-b border-slate-800">
                <div>
                    <div class="flex items-center gap-2 mb-1">
                        <span class="px-2.5 py-0.5 rounded text-xs font-bold bg-brand-500/20 text-brand-400 border border-brand-500/30">BAGIAN 1</span>
                        <h2 class="text-xl font-bold text-white">Visual Architecture & Data Flow Chart</h2>
                    </div>
                    <p class="text-slate-400 text-sm">Pemetaan visual komponen sistem dan alur data pemrosesan pesan & pengingat otomatis.</p>
                </div>
                <!-- Legend -->
                <div class="flex items-center gap-4 text-xs text-slate-400">
                    <span class="flex items-center gap-1.5"><span class="w-2.5 h-2.5 rounded-full bg-brand-500"></span> User Ingestion</span>
                    <span class="flex items-center gap-1.5"><span class="w-2.5 h-2.5 rounded-full bg-purple-500"></span> AI Parsing</span>
                    <span class="flex items-center gap-1.5"><span class="w-2.5 h-2.5 rounded-full bg-amber-500"></span> Auto-Reminder</span>
                </div>
            </div>

            <!-- VISUAL ARCHITECTURE BOARD (GRID LAYOUT) -->
            <div class="grid grid-cols-1 lg:grid-cols-12 gap-6 relative">

                <!-- 1. USER CLIENT LAYER (Col 1-3) -->
                <div class="lg:col-span-3 flex flex-col justify-between space-y-4">
                    <div class="glass-card rounded-xl p-5 border-l-4 border-l-emerald-500 h-full relative group">
                        <div class="flex items-center justify-between mb-4">
                            <span class="text-xs font-semibold uppercase tracking-wider text-emerald-400">User Layer</span>
                            <i data-lucide="smartphone" class="w-5 h-5 text-emerald-400"></i>
                        </div>
                        <h3 class="font-bold text-lg text-white mb-1">Registered User</h3>
                        <p class="text-xs text-slate-400 mb-4">Pengirim pesan via WhatsApp (Format teks bebas & Pertanyaan)</p>
                        
                        <div class="bg-slate-950/80 rounded-lg p-3 text-xs space-y-2 border border-slate-800 font-mono">
                            <div class="text-slate-400 flex items-center justify-between">
                                <span>Input Ingestion:</span>
                                <span class="text-emerald-400 text-[10px]">Active</span>
                            </div>
                            <div class="p-2 bg-emerald-950/40 rounded border border-emerald-800/40 text-emerald-200 text-[11px]">
                                "Tugas matematika diberikan tgl 8, deadline tgl 11"
                            </div>
                        </div>

                        <!-- Connector Arrow Right -->
                        <div class="hidden lg:flex absolute -right-3 top-1/2 -translate-y-1/2 z-20 w-6 h-6 bg-emerald-500 text-slate-950 rounded-full items-center justify-center shadow-lg">
                            <i data-lucide="arrow-right" class="w-4 h-4"></i>
                        </div>
                    </div>
                </div>

                <!-- 2. GATEWAY & MIDDLEWARE (Col 4-6) -->
                <div class="lg:col-span-3 flex flex-col justify-between space-y-4">
                    <div class="glass-card rounded-xl p-5 border-l-4 border-l-blue-500 h-full relative">
                        <div class="flex items-center justify-between mb-4">
                            <span class="text-xs font-semibold uppercase tracking-wider text-blue-400">Gateway Layer</span>
                            <i data-lucide="server" class="w-5 h-5 text-blue-400"></i>
                        </div>
                        <h3 class="font-bold text-lg text-white mb-1">Node.js Gateway</h3>
                        <p class="text-xs text-slate-400 mb-4">Service pengendali Client WhatsApp Session & Message Router.</p>
                        
                        <div class="space-y-2 text-xs">
                            <div class="bg-slate-900/90 p-2.5 rounded-lg border border-slate-800 flex items-center gap-2 text-slate-300">
                                <i data-lucide="cpu" class="w-4 h-4 text-blue-400"></i>
                                <span>whatsapp-web.js Listener</span>
                            </div>
                            <div class="bg-slate-900/90 p-2.5 rounded-lg border border-slate-800 flex items-center gap-2 text-slate-300">
                                <i data-lucide="send" class="w-4 h-4 text-blue-400"></i>
                                <span>Auto-Notifier Dispatcher</span>
                            </div>
                        </div>

                        <!-- Connector Arrow Right -->
                        <div class="hidden lg:flex absolute -right-3 top-1/2 -translate-y-1/2 z-20 w-6 h-6 bg-blue-500 text-slate-950 rounded-full items-center justify-center shadow-lg">
                            <i data-lucide="arrow-right" class="w-4 h-4"></i>
                        </div>
                    </div>
                </div>

                <!-- 3. AI ENGINE & DATABASE LAYER (Col 7-9) -->
                <div class="lg:col-span-3 flex flex-col gap-4">
                    <!-- AI Engine -->
                    <div class="glass-card rounded-xl p-4 border-l-4 border-l-purple-500 relative">
                        <div class="flex items-center justify-between mb-2">
                            <span class="text-xs font-semibold uppercase tracking-wider text-purple-400">AI Core Engine</span>
                            <i data-lucide="sparkles" class="w-4 h-4 text-purple-400"></i>
                        </div>
                        <h4 class="font-bold text-sm text-white mb-1">LLM Integration</h4>
                        <div class="text-[11px] text-slate-400 space-y-1">
                            <div class="flex items-center gap-1.5"><i data-lucide="check" class="w-3 h-3 text-purple-400"></i> Entity Extraction</div>
                            <div class="flex items-center gap-1.5"><i data-lucide="check" class="w-3 h-3 text-purple-400"></i> Structured JSON Output</div>
                            <div class="flex items-center gap-1.5"><i data-lucide="check" class="w-3 h-3 text-purple-400"></i> Priority Analysis Algorithm</div>
                        </div>
                    </div>

                    <!-- PostgreSQL DB -->
                    <div class="glass-card rounded-xl p-4 border-l-4 border-l-sky-500 relative">
                        <div class="flex items-center justify-between mb-2">
                            <span class="text-xs font-semibold uppercase tracking-wider text-sky-400">Database Layer</span>
                            <i data-lucide="database" class="w-4 h-4 text-sky-400"></i>
                        </div>
                        <h4 class="font-bold text-sm text-white mb-1">PostgreSQL DB</h4>
                        <div class="text-[11px] text-slate-400 space-y-1 font-mono">
                            <div>• users (id, phone, name)</div>
                            <div>• tasks (id, title, due_date, status)</div>
                            <div>• priority_logs (score, rank)</div>
                        </div>
                    </div>
                </div>

                <!-- 4. SCHEDULER & CRON SERVICE (Col 10-12) -->
                <div class="lg:col-span-3 flex flex-col justify-between space-y-4">
                    <div class="glass-card rounded-xl p-5 border-l-4 border-l-amber-500 h-full relative">
                        <div class="flex items-center justify-between mb-4">
                            <span class="text-xs font-semibold uppercase tracking-wider text-amber-400">Background Worker</span>
                            <i data-lucide="clock" class="w-5 h-5 text-amber-400"></i>
                        </div>
                        <h3 class="font-bold text-lg text-white mb-1">Cron Job Service</h3>
                        <p class="text-xs text-slate-400 mb-4">Sistem pemantau waktu & trigger pengingat tugas aktif.</p>

                        <div class="bg-amber-950/30 rounded-lg p-3 border border-amber-800/40 text-xs space-y-2">
                            <div class="flex items-center justify-between text-amber-300 font-semibold">
                                <span>Interval Checker</span>
                                <span class="bg-amber-500/20 text-amber-300 px-1.5 py-0.5 rounded text-[10px]">Periodik</span>
                            </div>
                            <p class="text-[11px] text-slate-300">Scan database setiap jam untuk deteksi H-1 & H-X jam mendekati deadline.</p>
                        </div>
                    </div>
                </div>

            </div>

            <!-- DETAILED DATA FLOW EXPLANATION CARDS (IN-GRAPHIC) -->
            <div class="mt-8 grid grid-cols-1 md:grid-cols-2 gap-4 pt-6 border-t border-slate-800">
                <!-- Flow A -->
                <div class="p-4 rounded-xl bg-slate-900/60 border border-slate-800 flex items-start gap-3">
                    <div class="p-2 rounded-lg bg-blue-500/10 text-blue-400 shrink-0">
                        <i data-lucide="arrow-right-left" class="w-5 h-5"></i>
                    </div>
                    <div>
                        <h5 class="text-xs font-bold text-blue-400 uppercase tracking-wider mb-1">Flow A: Message Parsing & Querying</h5>
                        <p class="text-xs text-slate-300">
                            Pesan natural user diterima oleh <code>whatsapp-web.js</code> $\rightarrow$ diteruskan ke <strong>LLM API</strong> untuk di-parse menjadi JSON/Intent $\rightarrow$ disimpan/diambil dari <strong>PostgreSQL</strong> $\rightarrow$ hasil respon rapi dikembalikan ke user.
                        </p>
                    </div>
                </div>

                <!-- Flow B -->
                <div class="p-4 rounded-xl bg-slate-900/60 border border-slate-800 flex items-start gap-3">
                    <div class="p-2 rounded-lg bg-amber-500/10 text-amber-400 shrink-0">
                        <i data-lucide="bell-ring" class="w-5 h-5"></i>
                    </div>
                    <div>
                        <h5 class="text-xs font-bold text-amber-400 uppercase tracking-wider mb-1">Flow B: Automatic Reminder Trigger</h5>
                        <p class="text-xs text-slate-300">
                            <strong>Cron Job Service</strong> mendeteksi tugas mendekati deadline di <strong>PostgreSQL</strong> $\rightarrow$ memicu trigger ke Gateway $\rightarrow$ bot secara aktif mengirim notifikasi WA ke nomor pengguna.
                        </p>
                    </div>
                </div>
            </div>

        </section>

        <!-- BAGIAN 2: PENJELASAN ARSITEKTUR DETAIL -->
        <section class="glass-panel rounded-2xl p-6 md:p-8 shadow-2xl">
            <div class="flex items-center gap-2 mb-6 pb-4 border-b border-slate-800">
                <span class="px-2.5 py-0.5 rounded text-xs font-bold bg-purple-500/20 text-purple-400 border border-purple-500/30">BAGIAN 2</span>
                <h2 class="text-xl font-bold text-white">Penjelasan Arsitektur & Spesifikasi Proyek Detail</h2>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-2 gap-6">

                <!-- Feature 1: Natural Language Parsing -->
                <div class="glass-card p-6 rounded-xl space-y-3">
                    <div class="w-10 h-10 rounded-lg bg-purple-500/10 border border-purple-500/20 flex items-center justify-center text-purple-400 mb-2">
                        <i data-lucide="brain-circuit" class="w-5 h-5"></i>
                    </div>
                    <h3 class="text-lg font-bold text-white">1. Natural Language Parsing (LLM Engine)</h3>
                    <p class="text-slate-300 text-sm leading-relaxed">
                        Pengguna tidak perlu mengetik perintah yang kaku. AI bertindak sebagai parser pintar yang membaca intent dan mengekstrak entitas penting.
                    </p>
                    <div class="bg-slate-950 p-3 rounded-lg border border-slate-800 text-xs text-slate-300 font-mono space-y-1">
                        <div class="text-purple-400 font-semibold">// Contoh Ekstraksi LLM</div>
                        <div>Input : "tugas matematika diberikan tgl 8, deadline tgl 11"</div>
                        <div class="text-emerald-400">Output: { "task": "matematika", "start_date": "2026-10-08", "due_date": "2026-10-11", "priority": "HIGH" }</div>
                    </div>
                </div>

                <!-- Feature 2: Database Schema & Task States -->
                <div class="glass-card p-6 rounded-xl space-y-3">
                    <div class="w-10 h-10 rounded-lg bg-sky-500/10 border border-sky-500/20 flex items-center justify-center text-sky-400 mb-2">
                        <i data-lucide="database" class="w-5 h-5"></i>
                    </div>
                    <h3 class="text-lg font-bold text-white">2. Skema Database & Relasi (PostgreSQL)</h3>
                    <p class="text-slate-300 text-sm leading-relaxed">
                        Sistem menggunakan relational database untuk menjaga integritas data user, tugas aktif, dan riwayat pengiriman reminder.
                    </p>
                    <ul class="text-xs text-slate-300 space-y-1.5 list-disc list-inside">
                        <li><strong class="text-white">users:</strong> Menyimpan nomor WA terdaftar & preferensi reminder.</li>
                        <li><strong class="text-white">tasks:</strong> Menyimpan ID, nama tugas, tanggal diberikan, deadline, dan status (PENDING, COMPLETED, OVERDUE).</li>
                        <li><strong class="text-white">reminder_logs:</strong> Mencegah spam pengingat ganda untuk tugas yang sama.</li>
                    </ul>
                </div>

                <!-- Feature 3: Cron Scheduler -->
                <div class="glass-card p-6 rounded-xl space-y-3">
                    <div class="w-10 h-10 rounded-lg bg-amber-500/10 border border-amber-500/20 flex items-center justify-center text-amber-400 mb-2">
                        <i data-lucide="alarm-clock" class="w-5 h-5"></i>
                    </div>
                    <h3 class="text-lg font-bold text-white">3. Cron Job & Automatic Reminder</h3>
                    <p class="text-slate-300 text-sm leading-relaxed">
                        Layanan latar belakang (background worker) yang berjalan secara periodik untuk mengecek tugas terdekat.
                    </p>
                    <div class="bg-slate-950 p-3 rounded-lg border border-slate-800 text-xs space-y-1 text-slate-300">
                        <div class="flex items-center gap-2 text-amber-400">
                            <i data-lucide="check-circle-2" class="w-4 h-4"></i> Trigger H-1 Hari Deadline
                        </div>
                        <div class="flex items-center gap-2 text-amber-400">
                            <i data-lucide="check-circle-2" class="w-4 h-4"></i> Trigger H-3 Jam Sebelum Deadline
                        </div>
                        <p class="text-[11px] text-slate-400 mt-1">Sistem otomatis mengirim pesan WA pengingat tanpa perlu dipicu aksi manual.</p>
                    </div>
                </div>

                <!-- Feature 4: Interactive Priority Query -->
                <div class="glass-card p-6 rounded-xl space-y-3">
                    <div class="w-10 h-10 rounded-lg bg-emerald-500/10 border border-emerald-500/20 flex items-center justify-center text-emerald-400 mb-2">
                        <i data-lucide="list-ordered" class="w-5 h-5"></i>
                    </div>
                    <h3 class="text-lg font-bold text-white">4. Query Interaktif & Skala Prioritas</h3>
                    <p class="text-slate-300 text-sm leading-relaxed">
                        User dapat sewaktu-waktu menanyakan daftar tugas aktif. AI akan mengambil data dari PostgreSQL dan mengurutkannya berdasarkan tingkat urgensi.
                    </p>
                    <div class="bg-slate-950 p-3 rounded-lg border border-slate-800 text-xs text-slate-300 font-mono">
                        <div class="text-slate-400">User: "informasi tugas"</div>
                        <div class="text-emerald-400 font-semibold mt-1">Bot Response:</div>
                        <div class="text-slate-200">📌 Tugas Aktif Terurut:</div>
                        <div>1. Matematika (Deadline: 11 Okt) - High</div>
                        <div>2. Sistem Informasi (Deadline: 12 Okt) - Medium</div>
                    </div>
                </div>

            </div>

            <!-- Tech Stack Summary Table -->
            <div class="mt-8 pt-6 border-t border-slate-800">
                <h3 class="text-base font-bold text-white mb-4 flex items-center gap-2">
                    <i data-lucide="layers" class="w-4 h-4 text-brand-400"></i> Ringkasan Teknologi Yang Digunakan
                </h3>
                <div class="overflow-x-auto">
                    <table class="w-full text-left text-xs text-slate-300 border-collapse">
                        <thead>
                            <tr class="border-b border-slate-800 bg-slate-900/50 text-slate-400">
                                <th class="p-3 font-semibold">Komponen</th>
                                <th class="p-3 font-semibold">Teknologi</th>
                                <th class="p-3 font-semibold">Fungsi Utama</th>
                            </tr>
                        </thead>
                        <tbody class="divide-y divide-slate-800/60">
                            <tr>
                                <td class="p-3 font-semibold text-emerald-400">WhatsApp Interface</td>
                                <td class="p-3 font-mono">whatsapp-web.js</td>
                                <td class="p-3">Mengelola sesi WhatsApp Web, mendengarkan pesan masuk, & pengiriman pesan balasan/notifikasi.</td>
                            </tr>
                            <tr>
                                <td class="p-3 font-semibold text-purple-400">AI / LLM Engine</td>
                                <td class="p-3 font-mono">OpenAI / Gemini API</td>
                                <td class="p-3">Ekstraksi bahasa alami (Natural Language), penentuan prioritas, dan format respon pesan cerdas.</td>
                            </tr>
                            <tr>
                                <td class="p-3 font-semibold text-sky-400">Database Layer</td>
                                <td class="p-3 font-mono">PostgreSQL</td>
                                <td class="p-3">Penyimpanan terstruktur data pengguna, detail tugas, deadline, serta log pengingat.</td>
                            </tr>
                            <tr>
                                <td class="p-3 font-semibold text-amber-400">Scheduler Service</td>
                                <td class="p-3 font-mono">Node.js + node-cron</td>
                                <td class="p-3">Menjalankan background job pemantau deadline dan memicu pengiriman pesan pengingat.</td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>

        </section>

        <!-- Footer -->
        <footer class="text-center text-xs text-slate-500 py-4 flex items-center justify-center gap-2">
            <span>Built with Node.js, WhatsApp-Web.js, LLM & PostgreSQL</span>
            <span>&bull;</span>
            <span>Task Manager Architecture Docs</span>
        </footer>

    </div>

    <!-- Initialize Lucide Icons -->
    <script>
        lucide.createIcons();
    </script>
</body>
</html>
