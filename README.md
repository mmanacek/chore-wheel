# chore-wheel
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

    <!-- HEADER BLOCK -->
    <header class="sticky top-0 z-40 bg-slate-950/90 backdrop-blur-md border-b border-slate-900 px-4 py-4">
        <div class="max-w-md mx-auto flex items-center justify-between">
            <div>
                <h1 class="text-xl font-black tracking-tight bg-gradient-to-r from-emerald-400 via-teal-400 to-cyan-400 bg-clip-text text-transparent">CHORE WHEEL</h1>
                <div class="flex items-center gap-2 mt-0.5">
                    <p id="current-date" class="text-xs text-slate-400 font-medium"></p>
                    <span id="digit-badge" class="text-[10px] uppercase font-extrabold px-2 py-0.5 rounded-md bg-slate-900 border border-slate-800 text-slate-300"></span>
                </div>
            </div>
            
            <!-- PROGRESS DIAL -->
            <div class="relative flex items-center justify-center w-12 h-12">
                <svg class="w-full h-full transform -rotate-90" viewBox="0 0 36 36">
                    <path class="text-slate-900" stroke-width="3.5" stroke="currentColor" fill="none" d="M18 2.0845 a 15.9155 15.9155 0 0 1 0 31.831 a 15.9155 15.9155 0 0 1 0 -31.831" />
                    <path id="progress-ring" class="text-emerald-400 transition-all duration-300 ease-out" stroke-dasharray="0, 100" stroke-width="3.5" stroke-linecap="round" stroke="currentColor" fill="none" d="M18 2.0845 a 15.9155 15.9155 0 0 1 0 31.831 a 15.9155 15.9155 0 0 1 0 -31.831" />
                </svg>
                <span id="progress-text" class="absolute text-[10px] font-black text-slate-200">0%</span>
            </div>
        </div>
    </header>

    <!-- MAIN INTERACTIVE CONTAINER -->
    <main class="flex-1 max-w-md w-full mx-auto p-4 pb-28 space-y-6 overflow-y-auto custom-scrollbar">
        
        <!-- SUNDAY FIBERMAXXING BRAINSTORMER MODULATOR -->
        <div id="fibermaxxing-card" class="hidden transform transition-all duration-300"></div>

        <!-- MORNING TIMELINE SECTION -->
        <div id="sec-morning" class="space-y-2.5">
            <div class="flex items-center gap-2 px-1 mb-1">
                <i data-lucide="sunrise" class="w-4 h-4 text-amber-400"></i>
                <h2 class="text-xs font-bold tracking-wider uppercase text-slate-400">Morning Routine</h2>
            </div>
            <div id="container-morning" class="space-y-2"></div>
        </div>

        <!-- AFTERNOON TIMELINE SECTION -->
        <div id="sec-afternoon" class="space-y-2.5">
            <div class="flex items-center gap-2 px-1 mb-1">
                <i data-lucide="sun" class="w-4 h-4 text-emerald-400"></i>
                <h2 class="text-xs font-bold tracking-wider uppercase text-slate-400">Afternoon Targets</h2>
            </div>
            <div id="container-afternoon" class="space-y-2"></div>
        </div>

        <!-- EVENING TIMELINE SECTION -->
        <div id="sec-evening" class="space-y-2.5">
            <div class="flex items-center gap-2 px-1 mb-1">
                <i data-lucide="moon" class="w-4 h-4 text-indigo-400"></i>
                <h2 class="text-xs font-bold tracking-wider uppercase text-slate-400">Evening Wrap-Up</h2>
            </div>
            <div id="container-evening" class="space-y-2"></div>
        </div>

    </main>

    <!-- DEBUG SYSTEM FOOTER -->
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

    <!-- ENGINE CODE -->
    <script>
        const baseDailyHabits = {
            morning: ["Get ready", "Wipe bathroom surfaces", "Moisturize", "Make bed", "Tidy 1 room"],
            evening: ["Dishes", "Wipe kitchen counters and table", "Spot clean kitchen/dining floors", "Floss"]
        };

        // Standardized, recognizable meal rotations that combine fiber, carbs, and simple proteins
        const fibermaxxingDatabase = [
            {
                ingredients: ["Black Beans", "Brown Rice", "Bell Peppers", "Chicken Breast", "Salsa"],
                menu: ["Rice and bean burrito bowls with chicken", "Stuffed bell peppers (ground beef, rice, and black beans)", "Simple sheet-pan chicken & pepper fajitas"]
            },
            {
                ingredients: ["Sweet Potatoes", "Pinto Beans", "Ground Turkey", "Broccoli", "Cheddar Cheese"],
                menu: ["Loaded baked sweet potatoes stuffed with pinto beans & turkey", "Ground turkey & broccoli stir-fry over brown rice", "Simple turkey chili with kidney beans"]
            },
            {
                ingredients: ["Brown Lentils", "Carrots", "Celery", "Canned Tomatoes", "Whole Wheat Pasta"],
                menu: ["Classic minestrone soup with whole wheat pasta", "Hearty lentil vegetable soup with whole wheat toast", "Baked chicken breasts with roasted carrots and red potatoes"]
            },
            {
                ingredients: ["Chickpeas", "Spinach", "Salmon or White Fish", "Quinoa", "Lemon & Garlic"],
                menu: ["Pan-seared fish with roasted broccoli and quinoa", "White bean, sausage, and spinach skillet stew", "Mediterranean chicken and chickpea rice bowls"]
            }
        ];

        let activeTrackingDate = new Date();

        document.addEventListener('DOMContentLoaded', () => {
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

        function processAppEngineCycle() {
            const currentDayNumber = activeTrackingDate.getDate();
            const lastDigit = currentDayNumber % 10;
            const dayOfWeek = activeTrackingDate.getDay(); 
            
            const options = { weekday: 'long', month: 'short', day: 'numeric' };
            document.getElementById('current-date').innerText = activeTrackingDate.toLocaleDateString('en-US', options);
            
            const badge = document.getElementById('digit-badge');
            badge.innerText = `Day Ending in [${lastDigit}]`;

            // Render Sunday Brainstormer Block Trigger
            renderSundayBrainstormer(dayOfWeek, currentDayNumber);

            const schedule = compileScheduleForDate(activeTrackingDate, currentDayNumber, lastDigit, dayOfWeek);
            renderTimelineColumns(schedule);
            updateProgressMetrics(schedule.totalTaskCount);
            lucide.createIcons();
        }

        function renderSundayBrainstormer(dayOfWeek, dayNum) {
            const card = document.getElementById('fibermaxxing-card');
            if (dayOfWeek === 0) { // 0 = Sunday
                const contentIndex = Math.floor(dayNum / 8) % fibermaxxingDatabase.length;
                const data = fibermaxxingDatabase[contentIndex];

                card.innerHTML = `
                    <div class="bg-gradient-to-br from-slate-900 to-slate-950 border border-emerald-500/20 rounded-2xl p-4 shadow-xl">
                        <div class="flex items-center gap-2 mb-3">
                            <i data-lucide="shopping-bag" class="w-4 h-4 text-emerald-400"></i>
                            <h3 class="text-xs font-black uppercase tracking-wider text-emerald-400">Weekly Meal Blueprint (Sunday Idea Generator)</h3>
                        </div>
                        <div class="space-y-3">
                            <div>
                                <span class="text-[10px] uppercase font-bold text-slate-400 block mb-1">Standard High-Fiber Grocery Targets</span>
                                <div class="flex flex-wrap gap-1">
                                    ${data.ingredients.map(ing => `<span class="text-[10px] px-2 py-0.5 rounded-md bg-emerald-500/10 text-emerald-300 font-medium border border-emerald-500/10">${ing}</span>`).join('')}
                                </div>
                            </div>
                            <div>
                                <span class="text-[10px] uppercase font-bold text-slate-400 block mb-1">Simple No-Fuss Classics</span>
                                <ul class="text-xs text-slate-300 space-y-1 list-disc list-inside">
                                    ${data.menu.map(item => `<li>${item}</li>`).join('')}
                                </ul>
                            </div>
                        </div>
                    </div>
                `;
                card.classList.remove('hidden');
            } else {
                card.classList.add('hidden');
            }
        }

        function compileScheduleForDate(date, dayNum, digit, dayOfWeek) {
            let morningTasks = [...baseDailyHabits.morning];
            let afternoonTasks = [];
            let eveningTasks = [...baseDailyHabits.evening];

            // FIXED DAY-OF-WEEK CAPACITY TASKS
            if ([0, 1, 3, 4, 5].includes(dayOfWeek)) { // Sun, Mon, Wed, Thu, Fri
                afternoonTasks.push("Water the garden");
            }
            if (dayOfWeek === 6) { // Saturday
                afternoonTasks.push("Mow the lawn");
            }

            // 10-DAY ROTATION LOGIC ENGINE
            switch(digit) {
                case 0:
                    morningTasks.push("Cut nails");
                    afternoonTasks.push("Rotate linens");
                    if (dayNum === 10) { 
                        afternoonTasks.push("Dust ceilings (1st of month exception)", "Dust lights (1st of month exception)");
                    }
                    break;
                case 1:
                    afternoonTasks.push("Take out compost", "Sweep pavement");
                    if (isAlternateDay1Occurrence(date)) {
                        afternoonTasks.push("Big weeding day (Bi-weekly deep sweep)");
                    } else {
                        afternoonTasks.push("Pull weeds");
                    }
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
                        afternoonTasks.push("Shoe maintenance");
                    } else {
                        afternoonTasks.push("Clean patio furniture (Bi-weekly alternative)");
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
                    if (dayNum === 8) afternoonTasks.push("Clear cobwebs from exterior crevasses");
                    if (dayNum === 18) afternoonTasks.push("Clean exterior of windows");
                    if (dayNum === 28) afternoonTasks.push("Clear cobwebs & touch up windows");
                    break;
                case 9:
                    afternoonTasks.push("Take out compost", "Sweep pavement", "Pull weeds");
                    break;
            }

            return {
                morning: morningTasks,
                afternoon: afternoonTasks,
                evening: eveningTasks,
                totalTaskCount: morningTasks.length + afternoonTasks.length + eveningTasks.length
            };
        }

        function isLastDayEndingIn2(date) {
            const workingDate = new Date(date.getTime());
            workingDate.setDate(workingDate.getDate() + 10);
            return workingDate.getMonth() !== date.getMonth();
        }

        function isAlternateDay4Occurrence(date) {
            let count = 0;
            let cursor = new Date(2026, 0, 1); 
            const targetTime = date.getTime();
            while (cursor.getTime() <= targetTime) {
                if (cursor.getDate() % 10 === 4) count++;
                cursor.setDate(cursor.getDate() + 1);
            }
            return count % 2 !== 0;
        }

        // Keep counting anchors locked to standard calendar grids
        function isAlternateDay1Occurrence(date) {
            let count = 0;
            let cursor = new Date(2026, 0, 1);
            const targetTime = date.getTime();
            while (cursor.getTime() <= targetTime) {
                if (cursor.getDate() % 10 === 1) count++;
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

            const categories = ['morning', 'afternoon', 'evening'];
            categories.forEach(cat => {
                const prefix = cat.charAt(0);
                const dom = schedule[cat].map((task, i) => {
                    const id = `${prefix}-${i}-${task.replace(/\s+/g, '_').toLowerCase()}`;
                    return generateTaskDomString(task, prefix, i, !!savedState[id]);
                }).join('');
                document.getElementById(`container-${cat}`).innerHTML = dom || `<p class="text-xs text-slate-600 pl-1 italic">No tasks listed.</p>`;
            });
        }

        function toggleTaskState(taskId) {
            const storageKey = getStorageKeyForDate(activeTrackingDate);
            let savedState = JSON.parse(localStorage.getItem(storageKey)) || {};
            
            savedState[taskId] = !savedState[taskId];
            localStorage.setItem(storageKey, JSON.stringify(savedState));

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

            const currentDayNumber = activeTrackingDate.getDate();
            const lastDigit = currentDayNumber % 10;
            const dayOfWeek = activeTrackingDate.getDay();
            const currentScheduleData = compileScheduleForDate(activeTrackingDate, currentDayNumber, lastDigit, dayOfWeek);
            
            updateProgressMetrics(currentScheduleData.totalTaskCount);
        }

        function updateProgressMetrics(totalCount) {
            const storageKey = getStorageKeyForDate(activeTrackingDate);
            const savedState = JSON.parse(localStorage.getItem(storageKey)) || {};
            
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
