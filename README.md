# chore-wheel
Chore Wheel and Personal Assistant
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Routine Tracker</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        body {
            -webkit-tap-highlight-color: transparent;
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
        }
        .custom-scrollbar::-webkit-scrollbar {
            display: none;
        }
    </style>
</head>
<body class="bg-slate-950 text-slate-100 min-h-screen flex flex-col antialiased selection:bg-emerald-500/30">

    <header class="sticky top-0 z-40 bg-slate-950/80 backdrop-blur-md border-b border-slate-800/60 px-4 py-4 safe-top">
        <div class="max-w-md mx-auto flex items-center justify-between">
            <div>
                <h1 class="text-xl font-bold tracking-tight bg-gradient-to-r from-emerald-400 to-teal-400 bg-clip-text text-transparent">Groom & Clean</h1>
                <p id="current-date" class="text-xs text-slate-400 mt-0.5 font-medium"></p>
            </div>
            <div class="flex items-center gap-3">
                <div class="relative flex items-center justify-center w-12 h-12">
                    <svg class="w-full h-full transform -rotate-90" viewBox="0 0 36 36">
                        <path class="text-slate-800" stroke-width="3" stroke="currentColor" fill="none" d="M18 2.0845 a 15.9155 15.9155 0 0 1 0 31.831 a 15.9155 15.9155 0 0 1 0 -31.831" />
                        <path id="progress-ring" class="text-emerald-400 transition-all duration-500 ease-out" stroke-dasharray="0, 100" stroke-width="3" stroke-linecap="round" stroke="currentColor" fill="none" d="M18 2.0845 a 15.9155 15.9155 0 0 1 0 31.831 a 15.9155 15.9155 0 0 1 0 -31.831" />
                    </svg>
                    <span id="progress-text" class="absolute text-[10px] font-bold text-slate-200">0%</span>
                </div>
            </div>
        </div>
    </header>

    <nav class="bg-slate-900/40 border-b border-slate-800/40 sticky top-[73px] z-30 backdrop-blur-md">
        <div class="max-w-md mx-auto flex justify-around p-1.5 gap-1">
            <button onclick="switchTab('daily')" id="tab-daily" class="flex-1 py-2 px-3 text-xs font-semibold rounded-xl transition-all duration-200 flex flex-col items-center gap-1 active:scale-95 text-emerald-400 bg-slate-800/80 shadow-sm border border-slate-700/30">
                <i data-lucide="sun" class="w-4 h-4"></i>
                Daily Habits
            </button>
            <button onclick="switchTab('rotation')" id="tab-rotation" class="flex-1 py-2 px-3 text-xs font-semibold rounded-xl transition-all duration-200 flex flex-col items-center gap-1 active:scale-95 text-slate-400 hover:text-slate-200">
                <i data-lucide="refresh-cw" class="w-4 h-4"></i>
                10-Day Rotation
            </button>
            <button onclick="switchTab('periodic')" id="tab-periodic" class="flex-1 py-2 px-3 text-xs font-semibold rounded-xl transition-all duration-200 flex flex-col items-center gap-1 active:scale-95 text-slate-400 hover:text-slate-200">
                <i data-lucide="calendar" class="w-4 h-4"></i>
                Periodic
            </button>
        </div>
    </nav>

    <main class="flex-1 max-w-md w-full mx-auto p-4 pb-24 overflow-y-auto custom-scrollbar">
        
        <div id="content-daily" class="tab-content space-y-6">
            <div>
                <div class="flex items-center gap-2 mb-3 px-1">
                    <i data-lucide="sunrise" class="w-4 h-4 text-amber-400"></i>
                    <h2 class="text-sm font-bold tracking-wide uppercase text-slate-400">Morning (The Foundation)</h2>
                </div>
                <div class="space-y-2.5" id="list-morning"></div>
            </div>

            <div>
                <div class="flex items-center gap-2 mb-3 px-1">
                    <i data-lucide="moon" class="w-4 h-4 text-indigo-400"></i>
                    <h2 class="text-sm font-bold tracking-wide uppercase text-slate-400">Afternoon & Evening</h2>
                </div>
                <div class="space-y-2.5" id="list-evening"></div>
            </div>
        </div>

        <div id="content-rotation" class="tab-content hidden space-y-4">
            <div class="bg-slate-900/60 border border-slate-800/80 rounded-2xl p-4 mb-2">
                <div class="flex items-start gap-3">
                    <i data-lucide="info" class="w-5 h-5 text-teal-400 mt-0.5 shrink-0"></i>
                    <div>
                        <h3 class="text-xs font-bold text-slate-200 uppercase tracking-wider mb-1">Rotation Rule Engine</h3>
                        <p class="text-xs text-slate-400 leading-relaxed">Odd tasks occur on odd calendar days (1, 3, 5, 7, 9). Even tasks balance your workload across the rest of the month.</p>
                    </div>
                </div>
            </div>
            
            <div>
                <div class="flex items-center justify-between mb-3 px-1">
                    <div class="flex items-center gap-2">
                        <i data-lucide="activity" class="w-4 h-4 text-teal-400"></i>
                        <h2 id="rotation-header" class="text-sm font-bold tracking-wide uppercase text-slate-400">Active Tasks</h2>
                    </div>
                    <span id="day-badge" class="text-[10px] uppercase tracking-wider font-extrabold px-2 py-0.5 rounded-full bg-slate-800 border border-slate-700 text-slate-300"></span>
                </div>
                <div class="space-y-2.5" id="list-rotation"></div>
            </div>
        </div>

        <div id="content-periodic" class="tab-content hidden space-y-6">
            <div>
                <div class="flex items-center gap-2 mb-3 px-1">
                    <i data-lucide="sparkles" class="w-4 h-4 text-emerald-400"></i>
                    <h2 class="text-sm font-bold tracking-wide uppercase text-slate-400">Weekly Tasks</h2>
                </div>
                <div class="space-y-2.5" id="list-weekly"></div>
            </div>

            <div>
                <div class="flex items-center gap-2 mb-3 px-1">
                    <i data-lucide="layers" class="w-4 h-4 text-purple-400"></i>
                    <h2 class="text-sm font-bold tracking-wide uppercase text-slate-400">Bi-Weekly Tasks</h2>
                </div>
                <div class="space-y-2.5" id="list-biweekly"></div>
            </div>

            <div>
                <div class="flex items-center gap-2 mb-3 px-1">
                    <i data-lucide="package" class="w-4 h-4 text-blue-400"></i>
                    <h2 class="text-sm font-bold tracking-wide uppercase text-slate-400">Monthly Targets (1st / Fixed Dates)</h2>
                </div>
                <div class="space-y-2.5" id="list-monthly"></div>
            </div>
        </div>

    </main>

    <footer class="fixed bottom-0 left-0 right-0 p-4 bg-gradient-to-t from-slate-950 via-slate-950/90 to-transparent z-40 pointer-events-none">
        <div class="max-w-md mx-auto flex justify-end pointer-events-auto">
            <button onclick="resetAppProgress()" class="flex items-center gap-2 px-4 py-3 bg-slate-900 border border-slate-800 hover:border-slate-700/80 active:scale-95 transition-all text-slate-300 text-xs font-semibold rounded-2xl shadow-xl backdrop-blur-md">
                <i data-lucide="rotate-ccw" class="w-3.5 h-3.5 text-slate-400"></i>
                Reset Checked Items
            </button>
        </div>
    </footer>

    <script>
        // Database parsed straight out of the system document
        const taskDatabase = {
            morning: [
                { id: 'm1', text: 'Get ready & personal care routine' },
                { id: 'm2', text: 'Wipe bathroom surfaces clear' },
                { id: 'm3', text: 'Skin care: moisturize' },
                { id: 'm4', text: 'Make bed' },
                { id: 'm5', text: 'Tidy up exactly 1 room completely' },
                { id: 'm6', text: 'Check nose / ear hair details' },
                { id: 'm7', text: 'Trim beard cleanly' }
            ],
            evening: [
                { id: 'e1', text: 'Wash dishes, clear kitchen counters & table' },
                { id: 'e2', text: 'Spot clean target floor surfaces' },
                { id: 'e3', text: 'Take out compost bin contents' },
                { id: 'e4', text: 'Sweep outdoor pavement paths' },
                { id: 'e5', text: 'Pull up stray garden weeds' },
                { id: 'e6', text: 'Fridge purge & quick microwave wipe down' }
            ],
            oddRotation: [
                { id: 'r1', text: 'Vacuum home & track key floor paths' },
                { id: 'r2', text: 'High touch surface sanitization sweep' }
            ],
            evenRotation: [
                { id: 'r3', text: 'Wipe out trash bin internals completely' }
            ],
            weekly: [
                { id: 'w1', text: 'Comprehensive surface dusting' },
                { id: 'w2', text: 'Nail care routine & clipping' }
            ],
            biweekly: [
                { id: 'b1', text: 'Shoe cleaning & leather maintenance' },
                { id: 'b2', text: 'Bed linens rotation & replacement' }
            ],
            monthly: [
                { id: 'mo1', text: 'Deep clean ceiling architectural fixtures & lights' }
            ]
        };

        let checkedState = JSON.parse(localStorage.getItem('routine_checked_v1')) || {};
        const today = new Date();
        const currentDayNum = today.getDate();
        const isOddDay = currentDayNum % 2 !== 0;

        // Initialize App UI
        document.addEventListener('DOMContentLoaded', () => {
            renderDateAndMeta();
            renderTasks();
            updateProgress();
            lucide.createIcons();
        });

        function renderDateAndMeta() {
            const options = { weekday: 'long', month: 'short', day: 'numeric' };
            document.getElementById('current-date').innerText = today.toLocaleDateString('en-US', options);
            
            const badge = document.getElementById('day-badge');
            const rotationHeader = document.getElementById('rotation-header');
            
            if (isOddDay) {
                badge.innerText = `Day ${currentDayNum}: ODD`;
                badge.className = "text-[10px] uppercase tracking-wider font-extrabold px-2 py-0.5 rounded-full bg-emerald-500/10 border border-emerald-500/20 text-emerald-400";
                rotationHeader.innerText = "Odd-Day Task Load";
            } else {
                badge.innerText = `Day ${currentDayNum}: EVEN`;
                badge.className = "text-[10px] uppercase tracking-wider font-extrabold px-2 py-0.5 rounded-full bg-blue-500/10 border border-blue-500/20 text-blue-400";
                rotationHeader.innerText = "Even-Day Task Load";
            }
        }

        function createTaskRow(task) {
            const isChecked = checkedState[task.id] || false;
            return `
                <div onclick="toggleTask('${task.id}')" class="flex items-center justify-between p-3.5 rounded-xl border border-slate-800/80 bg-slate-900/40 hover:bg-slate-900/70 transition-all duration-150 cursor-pointer group active:bg-slate-900">
                    <span class="text-xs font-medium text-slate-300 group-hover:text-slate-100 transition-colors pr-3 flex-1 select-none ${isChecked ? 'line-through !text-slate-500' : ''}" id="text-${task.id}">
                        ${task.text}
                    </span>
                    <div class="w-5 h-5 rounded-md border flex items-center justify-center transition-all shrink-0 ${isChecked ? 'bg-emerald-500 border-emerald-500 text-slate-950' : 'border-slate-700 bg-slate-950 group-hover:border-slate-500'}" id="box-${task.id}">
                        <i data-lucide="check" class="w-3.5 h-3.5 stroke-[3] ${isChecked ? 'block' : 'hidden'}"></i>
                    </div>
                </div>
            `;
        }

        function renderTasks() {
            document.getElementById('list-morning').innerHTML = taskDatabase.morning.map(createTaskRow).join('');
            document.getElementById('list-evening').innerHTML = taskDatabase.evening.map(createTaskRow).join('');
            document.getElementById('list-weekly').innerHTML = taskDatabase.weekly.map(createTaskRow).join('');
            document.getElementById('list-biweekly').innerHTML = taskDatabase.biweekly.map(createTaskRow).join('');
            document.getElementById('list-monthly').innerHTML = taskDatabase.monthly.map(createTaskRow).join('');
            
            // Render active rotation tasks depending on system dates
            const activeRotation = isOddDay ? taskDatabase.oddRotation : taskDatabase.evenRotation;
            document.getElementById('list-rotation').innerHTML = activeRotation.map(createTaskRow).join('');
        }

        function toggleTask(id) {
            checkedState[id] = !checkedState[id];
            localStorage.setItem('routine_checked_v1', JSON.stringify(checkedState));
            
            // Soft visual DOM adjustments without total re-render for responsive transitions
            const textElement = document.getElementById(`text-${id}`);
            const boxElement = document.getElementById(`box-${id}`);
            const checkIcon = boxElement.querySelector('i');

            if (checkedState[id]) {
                textElement.classList.add('line-through', '!text-slate-500');
                boxElement.className = "w-5 h-5 rounded-md border flex items-center justify-center transition-all shrink-0 bg-emerald-500 border-emerald-500 text-slate-950";
                if(checkIcon) checkIcon.classList.remove('hidden');
            } else {
                textElement.classList.remove('line-through', '!text-slate-500');
                boxElement.className = "w-5 h-5 rounded-md border flex items-center justify-center transition-all shrink-0 border-slate-700 bg-slate-950";
                if(checkIcon) checkIcon.classList.add('hidden');
            }

            updateProgress();
        }

        function updateProgress() {
            // Collect all currently rendered tasks inside DOM viewport targets
            const activeRotation = isOddDay ? taskDatabase.oddRotation : taskDatabase.evenRotation;
            const totalVisibleTasks = [
                ...taskDatabase.morning,
                ...taskDatabase.evening,
                ...activeRotation,
                ...taskDatabase.weekly,
                ...taskDatabase.biweekly,
                ...taskDatabase.monthly
            ];

            const totalCount = totalVisibleTasks.length;
            const completedCount = totalVisibleTasks.filter(t => checkedState[t.id]).length;
            
            const calculation = totalCount > 0 ? Math.round((completedCount / totalCount) * 100) : 0;
            
            document.getElementById('progress-text').innerText = `${calculation}%`;
            document.getElementById('progress-ring').setAttribute('stroke-dasharray', `${calculation}, 100`);
        }

        function switchTab(targetTab) {
            document.querySelectorAll('.tab-content').forEach(el => el.classList.add('hidden'));
            document.getElementById(`content-${targetTab}`).classList.remove('hidden');

            const tabs = ['daily', 'rotation', 'periodic'];
            tabs.forEach(tab => {
                const btn = document.getElementById(`tab-${tab}`);
                if (tab === targetTab) {
                    btn.className = "flex-1 py-2 px-3 text-xs font-semibold rounded-xl transition-all duration-200 flex flex-col items-center gap-1 active:scale-95 text-emerald-400 bg-slate-800/80 shadow-sm border border-slate-700/30";
                } else {
                    btn.className = "flex-1 py-2 px-3 text-xs font-semibold rounded-xl transition-all duration-200 flex flex-col items-center gap-1 active:scale-95 text-slate-400 hover:text-slate-200";
                }
            });
            // Re-render to pick up structural modifications accurately
            lucide.createIcons();
        }

        function resetAppProgress() {
            if(confirm("Reset all checked milestones to build fresh?")) {
                checkedState = {};
                localStorage.removeItem('routine_checked_v1');
                renderTasks();
                updateProgress();
                lucide.createIcons();
            }
        }
    </script>
</body>
</html>
