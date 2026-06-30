# chore-wheel
Chore Wheel and Personal Assistant
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Chore Wheel Engine</title>
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

    <header class="sticky top-0 z-40 bg-slate-950/90 backdrop-blur-md border-b border-slate-900 px-4 py-4">
        <div class="max-w-md mx-auto flex items-center justify-between">
            <div>
                <h1 class="text-xl font-black tracking-tight bg-gradient-to-r from-emerald-400 via-teal-400 to-cyan-400 bg-clip-text text-transparent">CHORE WHEEL</h1>
                <div class="flex items-center gap-2 mt-0.5">
                    <p id="current-date" class="text-xs text-slate-400 font-medium"></p>
                    <span id="digit-badge" class="text-[10px] uppercase font-extrabold px-2 py-0.5 rounded-md bg-slate-900 border border-slate-800 text-slate-300"></span>
                </div>
            </div>
            
            <div class="relative flex items-center justify-center w-12 h-12">
                <svg class="w-full h-full transform -rotate-90" viewBox="0 0 36 36">
                    <path class="text-slate-900" stroke-width="3.5" stroke="currentColor" fill="none" d="M18 2.0845 a 15.9155 15.9155 0 0 1 0 31.831 a 15.9155 15.9155 0 0 1 0 -31.831" />
                    <path id="progress-ring" class="text-emerald-400 transition-all duration-300 ease-out" stroke-dasharray="0, 100" stroke-width="3.5" stroke-linecap="round" stroke="currentColor" fill="none" d="M18 2.0845 a 15.9155 15.9155 0 0 1 0 31.831 a 15.9155 15.9155 0 0 1 0 -31.831" />
                </svg>
                <span id="progress-text" class="absolute text-[10px] font-black text-slate-200">0%</span>
            </div>
        </div>
    </header>

    <main class="flex-1 max-w-md w-full mx-auto p-4 pb-28 space-y-6 overflow-y-auto custom-scrollbar">
        
        <div id="sec-morning" class="space-y-2.5">
            <div class="flex items-center gap-2 px-1 mb-1">
                <i data-lucide="sunrise" class="w-4 h-4 text-amber-400"></i>
                <h2 class="text-xs font-bold tracking-wider uppercase text-slate-400">Morning Routine</h2>
            </div>
            <div id="container-morning" class="space-y-2"></div>
        </div>

        <div id="sec-afternoon" class="space-y-2.5">
            <div class="flex items-center gap-2 px-1 mb-1">
                <i data-lucide="sun" class="w-4 h-4 text-emerald-400"></i>
                <h2 class="text-xs font-bold tracking-wider uppercase text-slate-400">Afternoon Targets</h2>
            </div>
            <div id="container-afternoon" class="space-y-2"></div>
        </div>

        <div id="sec-evening" class="space-y-2.5">
            <div class="flex items-center gap-2 px-1 mb-1">
                <i data-lucide="moon" class="w-4 h-4 text-indigo-400"></i>
                <h2 class="text-xs font-bold tracking-wider uppercase text-slate-400">Evening Wrap-Up</h2>
            </div>
            <div id="container-evening" class="space-y-2"></div>
        </div>

    </main>

    <footer class="fixed bottom-0 left-0 right-0 p-4 bg-slate-950 border-t border-slate-900 z-40 bg-gradient-to-t from-slate-950 via-slate-950 to-slate-950/95">
        <div class="max-w-md mx-auto flex items-center justify-between gap-4">
            <div class="flex items-center gap-2 bg-slate-900 border border-slate-800 rounded-xl px-2.5 py-1.5 flex-1">
                <i data-lucide="sliders" class="w-3.5 h-3.5 text-slate-400 shrink-0"></i>
                <label for="date-simulator" class="text-[10px] font-bold uppercase text-slate-400 mr-1 select-none">Test Day:</label>
                <input type="date" id="date-simulator" class="bg-transparent text-xs text-slate-200 focus:outline-none flex-1 font-medium cursor-pointer" />
            </div>
            <button onclick="clearCurrentDayProgress()" class="px-3.5 py-2 bg-slate-900 border border-slate-800 active:scale-95 transition-all text-slate-400 hover:text-slate-200 text-xs font-semibold rounded-xl shrink-0 flex items-center gap-1.5">
                <i data-lucide="rotate-ccw" class="w-3.5 h-3.5"></i>
                Reset
            </button>
        </div>
    </footer>

    <script>
        // Core data objects mapped directly from plain text parameters
        const baseDailyHabits = {
            morning: [
                "Get ready",
                "Wipe bathroom surfaces",
                "Moisturize",
                "Make bed",
                "Tidy 1 room"
            ],
            evening: [
                "Dishes",
                "Wipe kitchen counters and table",
                "Spot clean kitchen/dining floors",
                "Floss"
            ]
        };

        // Date simulator logic variable initialization
        let activeTrackingDate = new Date();

        document.addEventListener('DOMContentLoaded', () => {
            // Preset simulated date input value field to today's local target
            const dateInput = document.getElementById('date-simulator');
            dateInput.value = activeTrackingDate.toISOString().split('T')[0];
            
            dateInput.addEventListener('change', (e) => {
                if(e.target.value) {
                    activeTrackingDate = new Date(e.target.value + 'T00:00:00');
                    processAppEngineCycle();
                }
            });

            processAppEngineCycle();
        });

        function getStorageKeyForDate(date) {
            return `chore_wheel_v2_${date.toISOString().split('T')[0]}`;
        }

        // Deep execution scheduler tracking engines
        function processAppEngineCycle() {
            const currentDayNumber = activeTrackingDate.getDate();
            const lastDigit = currentDayNumber % 10;
            
            // Render Header Text Strings
            const options = { weekday: 'long', month: 'short', day: 'numeric' };
            document.getElementById('current-date').innerText = activeTrackingDate.toLocaleDateString('en-US', options);
            
            const badge = document.getElementById('digit-badge');
            badge.innerText = `Day Ending in [${lastDigit}]`;

            // Compute structural arrays
            const schedule = compileScheduleForDate(activeTrackingDate, currentDayNumber, lastDigit);
            renderTimelineColumns(schedule);
            updateProgressMetrics(schedule.totalTaskCount);
            lucide.createIcons();
        }

        function compileScheduleForDate(date, dayNum, digit) {
            // Clone standard baseline daily requirements cleanly
            let morningTasks = [...baseDailyHabits.morning];
            let afternoonTasks = [];
            let eveningTasks = [...baseDailyHabits.evening];

            // 10-Day Sequence Rule Match Blocks
            switch(digit) {
                case 0:
                    morningTasks.push("Cut nails");
                    afternoonTasks.push("Rotate linens");
                    if (dayNum === 10) { 
                        afternoonTasks.push("Dust ceilings (1st of month exception)");
                        afternoonTasks.push("Dust lights (1st of month exception)");
                    }
                    break;
                case 1:
                    afternoonTasks.push("Take out compost", "Sweep pavement", "Pull weeds");
                    eveningTasks.push("Refrigerator purge");
                    break;
                case 2:
                    morningTasks.push("Trim beard");
                    afternoonTasks.push("Vacuum", "Sanitize high-touch surfaces");
                    if (isLastDayEndingIn2(date)) {
                        afternoonTasks.push("Wipe out trash bins (End of month exception)");
                    }
                    eveningTasks.push("Wipe microwave");
                    break;
                case 3:
                    afternoonTasks.push("Take out compost", "Sweep pavement", "Pull weeds");
                    break;
                case 4:
                    afternoonTasks.push("Dust surfaces");
                    if (isAlternateDay4Occurrence(date)) {
                        afternoonTasks.push("Shoe maintenance (Alternate day 4 choice)");
                    }
                    break;
                case 5:
                    afternoonTasks.push("Take out compost", "Sweep pavement", "Pull weeds");
                    break;
                case 6:
                    morningTasks.push("Trim beard");
                    afternoonTasks.push("Vacuum", "Sanitize high-touch surfaces");
                    break;
                case 7:
                    afternoonTasks.push("Take out compost", "Sweep pavement", "Pull weeds");
                    break;
                case 8:
                    morningTasks.push("Check nose hair", "Check ear hair", "Cut nails");
                    break;
                case 9:
                    afternoonTasks.push("Take out compost", "Sweep pavement", "Pull weeds");
                    break;
            }

            const totalCount = morningTasks.length + afternoonTasks.length + eveningTasks.length;

            return {
                morning: morningTasks,
                afternoon: afternoonTasks,
                evening: eveningTasks,
                totalTaskCount: totalCount
            };
        }

        // Helper calculations for calendar boundaries
        function isLastDayEndingIn2(date) {
            const workingDate = new Date(date.getTime());
            workingDate.setDate(workingDate.getDate() + 10);
            // If stepping forward 10 calendar days hits the next month, this execution block was the final day ending in 2
            return workingDate.getMonth() !== date.getMonth();
        }

        function isAlternateDay4Occurrence(date) {
            let count = 0;
            let cursor = new Date(2026, 0, 1); // Ground reference point tracking line 2026
            const targetTime = date.getTime();

            while (cursor.getTime() <= targetTime) {
                if (cursor.getDate() % 10 === 4) {
                    count++;
                }
                cursor.setDate(cursor.getDate() + 1);
            }
            return count % 2 !== 0;
        }

        function generateTaskDomString(taskText, categoryId, index, isChecked) {
            const uniqueTaskId = `${categoryId}-${index}-${taskText.replace(/\s+/g, '_').toLowerCase()}`;
            return `
                <div onclick="toggleTaskState('${uniqueTaskId}')" class="flex items-center justify-between p-3.5 rounded-xl border border-slate-900 bg-slate-900/30 hover:bg-slate-900/60 transition-all duration-150 cursor-pointer group active:bg-slate-900">
                    <span class="text-xs font-semibold text-slate-300 group-hover:text-slate-100 transition-colors pr-3 flex-1 select-none ${isChecked ? 'line-through !text-slate-600 font-medium' : ''}" id="text-${uniqueTaskId}">
                        ${taskText}
                    </span>
                    <div class="w-5 h-5 rounded-md border flex items-center justify-center transition-all shrink-0 ${isChecked ? 'bg-emerald-500 border-emerald-500 text-slate-950' : 'border-slate-800 bg-slate-950 group-hover:border-slate-600'}" id="box-${uniqueTaskId}">
                        <i data-lucide="check" class="w-3.5 h-3.5 stroke-[3.5] ${isChecked ? 'block' : 'hidden'}"></i>
                    </div>
                </div>
            `;
        }

        function renderTimelineColumns(schedule) {
            const storageKey = getStorageKeyForDate(activeTrackingDate);
            const savedState = JSON.parse(localStorage.getItem(storageKey)) || {};

            // Render columns
            const morningDom = schedule.morning.map((task, i) => {
                const id = `m-${i}-${task.replace(/\s+/g, '_').toLowerCase()}`;
                return generateTaskDomString(task, 'm', i, !!savedState[id]);
            }).join('');
            document.getElementById('container-morning').innerHTML = morningDom || `<p class="text-xs text-slate-600 pl-1 italic">No tasks listed.</p>`;

            const afternoonDom = schedule.afternoon.map((task, i) => {
                const id = `a-${i}-${task.replace(/\s+/g, '_').toLowerCase()}`;
                return generateTaskDomString(task, 'a', i, !!savedState[id]);
            }).join('');
            document.getElementById('container-afternoon').innerHTML = afternoonDom || `<p class="text-xs text-slate-600 pl-1 italic">No tasks listed.</p>`;

            const eveningDom = schedule.evening.map((task, i) => {
                const id = `e-${i}-${task.replace(/\s+/g, '_').toLowerCase()}`;
                return generateTaskDomString(task, 'e', i, !!savedState[id]);
            }).join('');
            document.getElementById('container-evening').innerHTML = eveningDom || `<p class="text-xs text-slate-600 pl-1 italic">No tasks listed.</p>`;
        }

        function toggleTaskState(taskId) {
            const storageKey = getStorageKeyForDate(activeTrackingDate);
            let savedState = JSON.parse(localStorage.getItem(storageKey)) || {};
            
            savedState[taskId] = !savedState[taskId];
            localStorage.setItem(storageKey, JSON.stringify(savedState));

            // Perform direct soft layout alterations to feel ultra snappy on mobile views
            const textElement = document.getElementById(`text-${taskId}`);
            const boxElement = document.getElementById(`box-${taskId}`);
            const checkIcon = boxElement.querySelector('i');

            if (savedState[taskId]) {
                textElement.classList.add('line-through', '!text-slate-600', 'font-medium');
                boxElement.className = "w-5 h-5 rounded-md border flex items-center justify-center transition-all shrink-0 bg-emerald-500 border-emerald-500 text-slate-950";
                if(checkIcon) checkIcon.classList.remove('hidden');
            } else {
                textElement.classList.remove('line-through', '!text-slate-600', 'font-medium');
                boxElement.className = "w-5 h-5 rounded-md border flex items-center justify-center transition-all shrink-0 border-slate-800 bg-slate-950";
                if(checkIcon) checkIcon.classList.add('hidden');
            }

            // Recalculate metrics totals dynamically
            const dateInput = document.getElementById('date-simulator');
            const targetDate = dateInput.value ? new Date(dateInput.value + 'T00:00:00') : new Date();
            const currentDayNumber = targetDate.getDate();
            const lastDigit = currentDayNumber % 10;
            const currentScheduleData = compileScheduleForDate(targetDate, currentDayNumber, lastDigit);
            
            updateProgressMetrics(currentScheduleData.totalTaskCount);
        }

        function updateProgressMetrics(totalCount) {
            const storageKey = getStorageKeyForDate(activeTrackingDate);
            const savedState = JSON.parse(localStorage.getItem(storageKey)) || {};
            
            // Count trues matching current structural IDs
            const completedCount = Object.values(savedState).filter(Boolean).length;
            const percentage = totalCount > 0 ? Math.round((completedCount / totalCount) * 100) : 0;

            document.getElementById('progress-text').innerText = `${percentage}%`;
            document.getElementById('progress-ring').setAttribute('stroke-dasharray', `${percentage}, 100`);
        }

        function clearCurrentDayProgress() {
            const storageKey = getStorageKeyForDate(activeTrackingDate);
            localStorage.removeItem(storageKey);
            processAppEngineCycle();
        }
    </script>
</body>
</html>
