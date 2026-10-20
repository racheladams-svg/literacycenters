<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Kindergarten Literacy Center Rotation Board</title>
  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>
  <!-- Google Fonts: Fredoka & Quicksand -->
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Fredoka:wght@400;600;700&family=Quicksand:wght@500;700&display=swap" rel="stylesheet">
  <!-- FontAwesome Icons -->
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  <!-- Canvas Confetti -->
  <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>

  <script>
    tailwind.config = {
      theme: {
        extend: {
          colors: {
            groupRed: '#FF4D4D',
            groupYellow: '#FFC107',
            groupOrange: '#FF8800',
            groupPurple: '#9C27B0',
            groupGreen: '#4CAF50',
          },
          fontFamily: {
            fredoka: ['Fredoka', 'sans-serif'],
            quicksand: ['Quicksand', 'sans-serif'],
          }
        }
      }
    }
  </script>

  <style>
    body {
      font-family: 'Fredoka', sans-serif;
      background: linear-gradient(135deg, #f0fdf4 0%, #e0f2fe 50%, #fef3c7 100%);
      min-height: 100vh;
      user-select: none;
    }

    /* Custom Scrollbar */
    ::-webkit-scrollbar {
      width: 8px;
      height: 8px;
    }
    ::-webkit-scrollbar-track {
      background: rgba(0, 0, 0, 0.05);
      border-radius: 10px;
    }
    ::-webkit-scrollbar-thumb {
      background: rgba(0, 0, 0, 0.2);
      border-radius: 10px;
    }

    .transition-card {
      transition: all 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
    }
    
    .student-badge {
      cursor: grab;
      touch-action: none;
    }
    .student-badge:active {
      cursor: grabbing;
    }

    .droppable-area.drag-over {
      background-color: rgba(254, 240, 138, 0.6) !important;
      border-style: dashed !important;
      transform: scale(1.02);
    }

    /* Ring progress meter */
    .progress-ring__circle {
      transition: stroke-dashoffset 0.35s;
      transform: rotate(-90deg);
      transform-origin: 50% 50%;
    }
  </style>
</head>
<body class="p-3 md:p-6 text-slate-800 flex flex-col min-h-screen">

  <!-- TOP HEADER & LITERACY BOOK ICON & LIVE DATE -->
  <header class="bg-white/95 backdrop-blur-md rounded-3xl p-4 shadow-xl mb-6 border-4 border-white flex flex-col lg:flex-row items-center justify-between gap-4">
    
    <!-- Title, Book Icon & Live Date -->
    <div class="flex items-center gap-4">
      <div class="w-16 h-16 bg-gradient-to-tr from-amber-400 via-rose-400 to-indigo-500 rounded-2xl flex items-center justify-center text-white text-3xl shadow-lg transform -rotate-3 hover:rotate-0 transition-transform">
        <i class="fa-solid fa-book-open"></i>
      </div>
      <div>
        <h1 class="text-2xl md:text-3xl font-extrabold text-slate-800 tracking-wide flex items-center gap-2 flex-wrap">
          Literacy Center Rotation Board
          <span id="rotationStageBadge" class="text-xs md:text-sm bg-indigo-600 text-white px-3 py-1 rounded-full uppercase tracking-wider font-bold">
            Rotation 1
          </span>
        </h1>
        <div class="flex items-center gap-2 text-xs md:text-sm text-slate-600 font-semibold mt-0.5">
          <i class="fa-solid fa-calendar-day text-indigo-500"></i>
          <input type="date" id="datePicker" onchange="updateDateDisplay(this.value)" class="bg-transparent hover:bg-slate-100 rounded px-1 cursor-pointer font-bold border border-transparent hover:border-slate-300">
          <span id="dateText" class="font-bold text-slate-700">Tuesday, October 20, 2026</span>
          <span class="text-slate-300">|</span>
          <i class="fa-regular fa-clock text-amber-500"></i>
          <span id="liveClock" class="font-bold font-mono text-slate-700">12:00:00 PM</span>
        </div>
      </div>
    </div>

    <!-- MAIN TIMER DISPLAY & CONTROLS -->
    <div class="flex items-center gap-3 bg-slate-100/90 p-2.5 px-4 rounded-2xl border-2 border-slate-200">
      <!-- Circular Progress Visual -->
      <div class="relative w-16 h-16 flex items-center justify-center">
        <svg class="w-16 h-16">
          <circle class="text-slate-200" stroke-width="6" stroke="currentColor" fill="transparent" r="26" cx="32" cy="32"/>
          <circle id="timerProgressCircle" class="progress-ring__circle text-indigo-600" stroke-width="6" stroke-dasharray="163.36" stroke-dashoffset="0" stroke-linecap="round" stroke="currentColor" fill="transparent" r="26" cx="32" cy="32"/>
        </svg>
        <i id="timerIcon" class="fa-solid fa-hourglass-half text-indigo-600 absolute text-lg"></i>
      </div>

      <!-- Time text -->
      <div class="flex flex-col min-w-[110px]">
        <span id="timerModeLabel" class="text-xs font-bold uppercase tracking-wider text-indigo-600">Rotation Time</span>
        <span id="timerDisplay" class="text-3xl md:text-4xl font-extrabold font-mono tracking-tight text-slate-800">12:00</span>
      </div>

      <!-- Quick Timer Adjusters (+/- 1 min) -->
      <div class="flex flex-col gap-1">
        <button onclick="adjustTimerSeconds(60)" class="bg-white hover:bg-slate-200 text-slate-700 font-bold px-2 py-0.5 rounded-lg border border-slate-300 text-xs shadow-sm active:scale-95" title="Add 1 Minute">
          +1m
        </button>
        <button onclick="adjustTimerSeconds(-60)" class="bg-white hover:bg-slate-200 text-slate-700 font-bold px-2 py-0.5 rounded-lg border border-slate-300 text-xs shadow-sm active:scale-95" title="Subtract 1 Minute">
          -1m
        </button>
      </div>

      <!-- Timer Controls -->
      <div class="flex items-center gap-1.5 ml-1">
        <button id="startPauseBtn" onclick="toggleTimer()" class="w-12 h-12 bg-emerald-500 hover:bg-emerald-600 active:scale-95 text-white rounded-xl shadow-lg flex items-center justify-center text-xl transition-all" title="Start / Pause Timer">
          <i id="startPauseIcon" class="fa-solid fa-play"></i>
        </button>
        <button onclick="resetTimer()" class="w-10 h-10 bg-slate-200 hover:bg-slate-300 active:scale-95 text-slate-700 rounded-xl flex items-center justify-center text-base transition-all" title="Reset Current Timer">
          <i class="fa-solid fa-rotate-left"></i>
        </button>
        <button onclick="skipTimerStage()" class="w-10 h-10 bg-amber-400 hover:bg-amber-500 active:scale-95 text-slate-900 rounded-xl flex items-center justify-center text-base transition-all" title="Skip to Transition / Next Phase">
          <i class="fa-solid fa-forward-step"></i>
        </button>
      </div>
    </div>

    <!-- ACTION BUTTONS -->
    <div class="flex items-center gap-2 flex-wrap justify-center">
      <button onclick="triggerAutoRotation()" class="bg-gradient-to-r from-blue-500 to-indigo-600 hover:from-blue-600 hover:to-indigo-700 text-white font-bold px-4 py-2.5 rounded-xl shadow-md active:scale-95 transition-all flex items-center gap-2 text-sm">
        <i class="fa-solid fa-arrows-rotate"></i>
        <span>Rotate Groups</span>
      </button>

      <button onclick="toggleGroupEditorModal()" class="bg-emerald-500 hover:bg-emerald-600 text-white font-bold px-4 py-2.5 rounded-xl shadow-md active:scale-95 transition-all flex items-center gap-2 text-sm">
        <i class="fa-solid fa-user-pen"></i>
        <span>Edit Groups & Names</span>
      </button>

      <button onclick="toggleRosterDrawer()" class="bg-amber-400 hover:bg-amber-500 text-slate-900 font-bold px-4 py-2.5 rounded-xl shadow-md active:scale-95 transition-all flex items-center gap-2 text-sm">
        <i class="fa-solid fa-users"></i>
        <span>Roster</span>
      </button>

      <button onclick="toggleEditMode()" id="editModeBtn" class="bg-slate-800 hover:bg-slate-900 text-white font-bold px-4 py-2.5 rounded-xl shadow-md active:scale-95 transition-all flex items-center gap-2 text-sm">
        <i class="fa-solid fa-gear"></i>
        <span>Settings</span>
      </button>

      <button onclick="confirmResetDaily()" class="bg-rose-100 hover:bg-rose-200 text-rose-700 font-bold px-3 py-2.5 rounded-xl active:scale-95 transition-all text-sm" title="Reset Rotation Progress back to Rotation 1">
        <i class="fa-solid fa-power-off"></i>
      </button>
    </div>
  </header>

  <!-- CLEAN UP & TRANSITION BANNER (VISIBLE DURING 2-MIN CLEANUP) -->
  <div id="cleanUpBanner" class="hidden mb-6 bg-gradient-to-r from-amber-400 via-orange-400 to-rose-400 text-slate-900 p-4 rounded-2xl shadow-xl flex items-center justify-between animate-bounce">
    <div class="flex items-center gap-4">
      <div class="text-4xl"><i class="fa-solid fa-broom"></i></div>
      <div>
        <h2 class="text-2xl font-black uppercase tracking-wider">Clean Up & Transition Time!</h2>
        <p class="font-semibold text-slate-800">Tidy up station materials, clean up iPads & journals, and prepare to move to your next station!</p>
      </div>
    </div>
    <button onclick="skipTimerStage()" class="bg-white text-slate-900 font-bold px-5 py-2.5 rounded-xl shadow-md hover:bg-slate-100 active:scale-95 transition-all">
      Start Next Rotation Now <i class="fa-solid fa-arrow-right ml-1"></i>
    </button>
  </div>

  <!-- CENTER STATIONS GRID -->
  <main class="flex-1">
    <div id="stationsGrid" class="grid grid-cols-1 md:grid-cols-2 xl:grid-cols-3 2xl:grid-cols-4 gap-4 md:gap-6">
      <!-- Dynamic Stations Loaded Here via JS -->
    </div>
  </main>

  <!-- EDIT GROUPS & STUDENTS MODAL -->
  <div id="groupEditorModal" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-50 flex items-center justify-center p-4 hidden">
    <div class="bg-white rounded-3xl max-w-4xl w-full max-h-[90vh] flex flex-col shadow-2xl border-4 border-white overflow-hidden">
      <div class="p-4 bg-emerald-600 text-white flex items-center justify-between">
        <div class="flex items-center gap-2">
          <i class="fa-solid fa-user-pen text-2xl"></i>
          <div>
            <h2 class="text-xl font-bold">Edit Color Groups & Student Names</h2>
            <p class="text-xs text-emerald-100">Add students, reassign group colors, or modify station assignments directly.</p>
          </div>
        </div>
        <button onclick="toggleGroupEditorModal()" class="text-emerald-100 hover:text-white p-1 rounded-lg text-2xl">
          <i class="fa-solid fa-xmark"></i>
        </button>
      </div>

      <div class="p-6 overflow-y-auto flex-1 space-y-6 bg-slate-50">
        <!-- Add Student Quick Bar -->
        <div class="bg-white p-4 rounded-2xl border-2 border-emerald-200 shadow-sm flex flex-col sm:flex-row items-center gap-3">
          <div class="flex-1 w-full">
            <label class="block text-xs font-bold uppercase text-slate-500 mb-1">Student Name</label>
            <input type="text" id="quickAddStudentName" placeholder="Enter student name..." class="w-full px-3 py-2 rounded-xl border-2 border-slate-300 font-bold focus:outline-none focus:border-emerald-500 text-sm">
          </div>
          <div class="w-full sm:w-auto">
            <label class="block text-xs font-bold uppercase text-slate-500 mb-1">Color Group</label>
            <select id="quickAddStudentGroup" class="w-full px-3 py-2 rounded-xl border-2 border-slate-300 font-bold focus:outline-none focus:border-emerald-500 text-sm bg-white">
              <option value="grp-red">🔴 Red Group</option>
              <option value="grp-yellow">🟡 Yellow Group</option>
              <option value="grp-orange">🟠 Orange Group</option>
              <option value="grp-purple">🟣 Purple Group</option>
              <option value="grp-green">🟢 Green Group</option>
              <option value="none">No Group Assigned</option>
            </select>
          </div>
          <button onclick="quickAddStudent()" class="w-full sm:w-auto mt-auto bg-emerald-600 hover:bg-emerald-700 text-white font-bold px-5 py-2.5 rounded-xl shadow-md transition-all active:scale-95 text-sm flex items-center justify-center gap-2">
            <i class="fa-solid fa-plus"></i> Add Student
          </button>
        </div>

        <!-- Color Groups Editor Cards -->
        <div id="groupEditorGrid" class="grid grid-cols-1 md:grid-cols-2 gap-4">
          <!-- Dynamically filled with group editors -->
        </div>
      </div>

      <div class="p-4 bg-slate-100 border-t border-slate-200 flex justify-between items-center">
        <span class="text-xs font-bold text-slate-500"><i class="fa-solid fa-floppy-disk mr-1"></i> Saves automatically</span>
        <button onclick="toggleGroupEditorModal()" class="bg-slate-800 hover:bg-slate-900 text-white font-bold px-6 py-2.5 rounded-xl shadow-md transition-all">
          Done Editing
        </button>
      </div>
    </div>
  </div>

  <!-- ROSTER DRAWER (SLIDE OVER) -->
  <div id="rosterDrawer" class="fixed inset-y-0 right-0 w-full max-w-md bg-white shadow-2xl z-50 transform translate-x-full transition-transform duration-300 flex flex-col">
    <div class="p-4 bg-indigo-600 text-white flex items-center justify-between">
      <div class="flex items-center gap-2">
        <i class="fa-solid fa-users text-xl"></i>
        <h2 class="text-xl font-bold">Class Roster & Holding Box</h2>
      </div>
      <button onclick="toggleRosterDrawer()" class="text-white hover:bg-indigo-700 p-2 rounded-lg text-xl">
        <i class="fa-solid fa-xmark"></i>
      </button>
    </div>

    <div class="p-4 bg-slate-50 border-b border-slate-200">
      <label class="block text-xs font-bold uppercase tracking-wider text-slate-500 mb-2">Add New Student</label>
      <div class="flex gap-2">
        <input type="text" id="newStudentName" placeholder="Enter student name..." class="flex-1 px-3 py-2 rounded-xl border-2 border-slate-300 focus:border-indigo-500 focus:outline-none font-semibold">
        <button onclick="addNewStudentFromDrawer()" class="bg-indigo-600 hover:bg-indigo-700 text-white font-bold px-4 py-2 rounded-xl active:scale-95 transition-all">
          <i class="fa-solid fa-plus"></i> Add
        </button>
      </div>
    </div>

    <div class="flex-1 p-4 overflow-y-auto">
      <h3 class="text-sm font-bold text-slate-600 uppercase tracking-wider mb-2 flex items-center justify-between">
        <span>Unassigned / Holding Box</span>
        <span id="unassignedCount" class="bg-slate-200 text-slate-700 text-xs px-2 py-0.5 rounded-full">0</span>
      </h3>
      <p class="text-xs text-slate-400 mb-3">Drag students directly into station boxes on the board.</p>
      
      <!-- Unassigned Box -->
      <div id="unassignedBox" ondrop="handleDrop(event, 'unassigned')" ondragover="handleDragOver(event)" ondragleave="handleDragLeave(event)" class="droppable-area min-h-[120px] p-3 bg-slate-100 rounded-2xl border-2 border-dashed border-slate-300 flex flex-wrap gap-2 content-start transition-all">
        <!-- Unassigned student tags injected here -->
      </div>

      <div class="mt-6 border-t border-slate-200 pt-4">
        <h3 class="text-sm font-bold text-slate-600 uppercase tracking-wider mb-2">Full Roster Management</h3>
        <div id="fullRosterList" class="space-y-2 max-h-60 overflow-y-auto pr-1">
          <!-- Roster item settings -->
        </div>
      </div>
    </div>

    <div class="p-4 bg-slate-100 border-t border-slate-200 text-center">
      <button onclick="toggleRosterDrawer()" class="w-full bg-slate-800 text-white font-bold py-2.5 rounded-xl shadow hover:bg-slate-900 transition-all">
        Done / Close Drawer
      </button>
    </div>
  </div>

  <!-- SETTINGS MODAL -->
  <div id="settingsModal" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-50 flex items-center justify-center p-4 hidden">
    <div class="bg-white rounded-3xl max-w-2xl w-full max-h-[90vh] flex flex-col shadow-2xl border-4 border-white overflow-hidden">
      <div class="p-4 bg-slate-800 text-white flex items-center justify-between">
        <h2 class="text-xl font-bold flex items-center gap-2">
          <i class="fa-solid fa-sliders text-amber-400"></i> Board Settings & Timer Setup
        </h2>
        <button onclick="toggleEditMode()" class="text-slate-400 hover:text-white p-1 rounded-lg text-xl">
          <i class="fa-solid fa-xmark"></i>
        </button>
      </div>

      <div class="p-6 overflow-y-auto space-y-6 flex-1">
        <!-- Timer Configuration -->
        <div class="bg-indigo-50 p-4 rounded-2xl border-2 border-indigo-100 space-y-4">
          <h3 class="font-bold text-indigo-900 flex items-center gap-2">
            <i class="fa-regular fa-clock"></i> Default Timer Durations
          </h3>
          <div class="grid grid-cols-1 sm:grid-cols-2 gap-4">
            <div>
              <label class="block text-xs font-bold uppercase text-indigo-700 mb-1">Rotation Duration (Minutes)</label>
              <input type="number" id="settingRotationMins" min="1" max="60" value="12" class="w-full px-3 py-2 rounded-xl border-2 border-indigo-200 font-bold focus:outline-none focus:border-indigo-500">
            </div>
            <div>
              <label class="block text-xs font-bold uppercase text-indigo-700 mb-1">Transition Duration (Minutes)</label>
              <input type="number" id="settingCleanUpMins" min="1" max="30" value="2" class="w-full px-3 py-2 rounded-xl border-2 border-indigo-200 font-bold focus:outline-none focus:border-indigo-500">
            </div>
          </div>
          <div class="flex items-center gap-2 pt-2">
            <input type="checkbox" id="settingAutoStartNext" class="w-4 h-4 text-indigo-600 rounded focus:ring-indigo-500" checked>
            <label for="settingAutoStartNext" class="text-xs font-bold text-indigo-900">Automatically start timer for next rotation after 2-min transition finishes</label>
          </div>
        </div>

        <!-- Add New Station -->
        <div class="bg-amber-50 p-4 rounded-2xl border-2 border-amber-100 space-y-3">
          <h3 class="font-bold text-amber-900 flex items-center gap-2">
            <i class="fa-solid fa-plus-circle"></i> Add Custom Station
          </h3>
          <div class="grid grid-cols-1 sm:grid-cols-2 gap-2">
            <input type="text" id="newStationTitle" placeholder="Station Title (e.g. Guided Reading)" class="px-3 py-2 rounded-xl border-2 border-amber-200 font-semibold text-sm">
            <input type="text" id="newStationTask" placeholder="Task Description" class="px-3 py-2 rounded-xl border-2 border-amber-200 font-semibold text-sm">
          </div>
          <div class="flex items-center gap-2">
            <select id="newStationIcon" class="px-3 py-2 rounded-xl border-2 border-amber-200 font-semibold text-sm bg-white">
              <option value="fa-book-open">📖 Book / Reading</option>
              <option value="fa-font">🔤 Letter Work</option>
              <option value="fa-tablet-screen-button">📱 iPad / Tech</option>
              <option value="fa-headphones">🎧 Audio / Listening</option>
              <option value="fa-pen-nib">✏️ Journal / Writing</option>
              <option value="fa-user-tie">👩‍🏫 Teacher Table</option>
              <option value="fa-shapes">🧩 Table / General</option>
            </select>
            <select id="newStationColor" class="px-3 py-2 rounded-xl border-2 border-amber-200 font-semibold text-sm bg-white">
              <option value="bg-red-500">Red Theme</option>
              <option value="bg-amber-400">Yellow Theme</option>
              <option value="bg-orange-500">Orange Theme</option>
              <option value="bg-purple-600">Purple Theme</option>
              <option value="bg-emerald-500">Green Theme</option>
              <option value="bg-blue-500">Blue Theme</option>
            </select>
            <button onclick="addNewStation()" class="bg-amber-500 hover:bg-amber-600 text-white font-bold px-4 py-2 rounded-xl text-sm transition-all ml-auto">
              Add Station
            </button>
          </div>
        </div>

        <!-- Station Management List -->
        <div>
          <h3 class="font-bold text-slate-700 uppercase tracking-wider text-xs mb-3">Manage Existing Stations</h3>
          <div id="settingsStationsList" class="space-y-3">
            <!-- Dynamic station list items -->
          </div>
        </div>
      </div>

      <div class="p-4 bg-slate-100 border-t border-slate-200 flex justify-end gap-2">
        <button onclick="saveSettings()" class="bg-indigo-600 hover:bg-indigo-700 text-white font-bold px-6 py-2.5 rounded-xl shadow-md transition-all">
          Save Settings
        </button>
      </div>
    </div>
  </div>

  <!-- FOOTER -->
  <footer class="mt-8 text-center text-xs text-slate-500 font-semibold py-2">
    <span>📚 Kindergarten Literacy Center Rotation Board &bull; Smartboard Ready &bull; Auto-saves locally</span>
  </footer>

  <!-- APPLICATION LOGIC -->
  <script>
    // --- STATIONS INITIAL SETUP ---
    const DEFAULT_STATIONS = [
      { id: 'stn-haigler', name: "Haigler's Table", color: 'bg-blue-500', border: 'border-blue-500', icon: 'fa-user-tie', task: 'Teacher Small Group Guidance' },
      { id: 'stn-sandy', name: "Ms. Sandy's Table", color: 'bg-pink-500', border: 'border-pink-500', icon: 'fa-user-tie', task: 'Teacher Small Group Guidance' },
      { id: 'stn-red', name: 'Red Table', color: 'bg-red-500', border: 'border-red-500', icon: 'fa-table-cells', task: 'Independent / Group Table Work' },
      { id: 'stn-yellow', name: 'Yellow Table', color: 'bg-amber-400', border: 'border-amber-400', icon: 'fa-table-cells', task: 'Independent / Group Table Work' },
      { id: 'stn-orange', name: 'Orange Table', color: 'bg-orange-500', border: 'border-orange-500', icon: 'fa-table-cells', task: 'Independent / Group Table Work' },
      { id: 'stn-purple', name: 'Purple Table', color: 'bg-purple-600', border: 'border-purple-600', icon: 'fa-table-cells', task: 'Independent / Group Table Work' },
      { id: 'stn-green', name: 'Green Table', color: 'bg-emerald-500', border: 'border-emerald-500', icon: 'fa-table-cells', task: 'Independent / Group Table Work' },
      { id: 'stn-ipad', name: 'iPad Station', color: 'bg-sky-500', border: 'border-sky-500', icon: 'fa-tablet-screen-button', task: 'iReady Reading Complete 2 lessons' },
      { id: 'stn-listening', name: 'Listening Station', color: 'bg-indigo-500', border: 'border-indigo-500', icon: 'fa-headphones', task: 'iPad EPIC Plus story time' },
      { id: 'stn-letter', name: 'Letter Work', color: 'bg-teal-500', border: 'border-teal-500', icon: 'fa-font', task: 'Match uppercase and lowercase letters and practice letter formation' },
      { id: 'stn-journal', name: 'Journal', color: 'bg-rose-500', border: 'border-rose-500', icon: 'fa-book-open', task: 'Draw pictures of certain sounds and label' }
    ];

    // --- STUDENT GROUPS SETUP ---
    const GROUPS = [
      { id: 'grp-red', name: 'Red Group', badgeColor: 'bg-red-500 text-white', defaultStation: 'stn-red' },
      { id: 'grp-yellow', name: 'Yellow Group', badgeColor: 'bg-amber-400 text-slate-900', defaultStation: 'stn-yellow' },
      { id: 'grp-orange', name: 'Orange Group', badgeColor: 'bg-orange-500 text-white', defaultStation: 'stn-orange' },
      { id: 'grp-purple', name: 'Purple Group', badgeColor: 'bg-purple-600 text-white', defaultStation: 'stn-purple' },
      { id: 'grp-green', name: 'Green Group', badgeColor: 'bg-emerald-500 text-white', defaultStation: 'stn-green' }
    ];

    const DEFAULT_STUDENTS = [
      { id: 'std-1', name: 'Emma', groupId: 'grp-red', stationId: 'stn-red' },
      { id: 'std-2', name: 'Liam', groupId: 'grp-red', stationId: 'stn-red' },
      { id: 'std-3', name: 'Noah', groupId: 'grp-yellow', stationId: 'stn-yellow' },
      { id: 'std-4', name: 'Olivia', groupId: 'grp-yellow', stationId: 'stn-yellow' },
      { id: 'std-5', name: 'Ava', groupId: 'grp-orange', stationId: 'stn-orange' },
      { id: 'std-6', name: 'Ethan', groupId: 'grp-orange', stationId: 'stn-orange' },
      { id: 'std-7', name: 'Lucas', groupId: 'grp-purple', stationId: 'stn-purple' },
      { id: 'std-8', name: 'Sophia', groupId: 'grp-purple', stationId: 'stn-purple' },
      { id: 'std-9', name: 'Jackson', groupId: 'grp-green', stationId: 'stn-green' },
      { id: 'std-10', name: 'Mia', groupId: 'grp-green', stationId: 'stn-green' }
    ];

    // --- STATE VARIABLES ---
    let stations = [];
    let students = [];
    let rotationIndex = 1;
    let isCleanUpPhase = false;
    let rotationDurationMins = 12;
    let cleanUpDurationMins = 2;
    let autoStartNextRotation = true;

    let timeRemainingSec = 12 * 60;
    let timerInterval = null;
    let isTimerRunning = false;
    let draggedStudentId = null;

    // --- AUDIO CHIME GENERATOR ---
    const AudioContext = window.AudioContext || window.webkitAudioContext;
    let audioCtx = null;

    function playChime(type = 'transition') {
      try {
        if (!audioCtx) audioCtx = new AudioContext();
        if (audioCtx.state === 'suspended') audioCtx.resume();

        const now = audioCtx.currentTime;
        if (type === 'transition') {
          // Clean Up Bell Bell Chime (E5, G5, C6)
          [659.25, 783.99, 1046.50].forEach((freq, idx) => {
            const osc = audioCtx.createOscillator();
            const gain = audioCtx.createGain();
            osc.type = 'sine';
            osc.frequency.setValueAtTime(freq, now + idx * 0.18);
            gain.gain.setValueAtTime(0.3, now + idx * 0.18);
            gain.gain.exponentialRampToValueAtTime(0.001, now + idx * 0.18 + 0.6);
            osc.connect(gain);
            gain.connect(audioCtx.destination);
            osc.start(now + idx * 0.18);
            osc.stop(now + idx * 0.18 + 0.6);
          });
        } else if (type === 'complete') {
          // Fanfare
          [523.25, 659.25, 783.99, 1046.50].forEach((freq, idx) => {
            const osc = audioCtx.createOscillator();
            const gain = audioCtx.createGain();
            osc.type = 'triangle';
            osc.frequency.setValueAtTime(freq, now + idx * 0.12);
            gain.gain.setValueAtTime(0.4, now + idx * 0.12);
            gain.gain.exponentialRampToValueAtTime(0.001, now + idx * 0.12 + 0.5);
            osc.connect(gain);
            gain.connect(audioCtx.destination);
            osc.start(now + idx * 0.12);
            osc.stop(now + idx * 0.12 + 0.5);
          });
        }
      } catch (e) {
        console.log("Audio unable to play automatically:", e);
      }
    }

    // --- LOCAL STORAGE PERSISTENCE ---
    function loadState() {
      const saved = localStorage.getItem('kg_literacy_center_rotation_data_v2');
      if (saved) {
        try {
          const parsed = JSON.parse(saved);
          stations = parsed.stations || DEFAULT_STATIONS;
          students = parsed.students || DEFAULT_STUDENTS;
          rotationIndex = parsed.rotationIndex || 1;
          isCleanUpPhase = parsed.isCleanUpPhase || false;
          rotationDurationMins = parsed.rotationDurationMins || 12;
          cleanUpDurationMins = parsed.cleanUpDurationMins || 2;
          autoStartNextRotation = parsed.autoStartNextRotation !== undefined ? parsed.autoStartNextRotation : true;
          timeRemainingSec = parsed.timeRemainingSec !== undefined ? parsed.timeRemainingSec : rotationDurationMins * 60;
          return;
        } catch (e) {
          console.error("Failed to parse saved state", e);
        }
      }
      // Defaults
      stations = [...DEFAULT_STATIONS];
      students = [...DEFAULT_STUDENTS];
      rotationIndex = 1;
      isCleanUpPhase = false;
      rotationDurationMins = 12;
      cleanUpDurationMins = 2;
      autoStartNextRotation = true;
      timeRemainingSec = rotationDurationMins * 60;
    }

    function saveState() {
      const payload = {
        stations,
        students,
        rotationIndex,
        isCleanUpPhase,
        rotationDurationMins,
        cleanUpDurationMins,
        autoStartNextRotation,
        timeRemainingSec
      };
      localStorage.setItem('kg_literacy_center_rotation_data_v2', JSON.stringify(payload));
    }

    // --- INITIALIZATION ---
    window.onload = function() {
      loadState();
      initLiveClockAndDate();
      renderBoard();
      updateTimerUI();
    };

    function initLiveClockAndDate() {
      // Set today's date into picker & display text
      const today = new Date();
      const yyyy = today.getFullYear();
      const mm = String(today.getMonth() + 1).padStart(2, '0');
      const dd = String(today.getDate()).padStart(2, '0');
      
      const datePicker = document.getElementById('datePicker');
      datePicker.value = `${yyyy}-${mm}-${dd}`;
      updateDateDisplay(datePicker.value);

      // Start live clock
      setInterval(() => {
        const now = new Date();
        document.getElementById('liveClock').innerText = now.toLocaleTimeString([], { hour: '2-digit', minute: '2-digit', second: '2-digit' });
      }, 1000);
    }

    function updateDateDisplay(dateVal) {
      if (!dateVal) return;
      const parts = dateVal.split('-');
      const d = new Date(parts[0], parts[1] - 1, parts[2]);
      const options = { weekday: 'long', month: 'long', day: 'numeric', year: 'numeric' };
      document.getElementById('dateText').innerText = d.toLocaleDateString('en-US', options);
    }

    // --- RENDER MAIN BOARD ---
    function renderBoard() {
      const container = document.getElementById('stationsGrid');
      container.innerHTML = '';

      stations.forEach((stn) => {
        const assignedStudents = students.filter(s => s.stationId === stn.id);

        const card = document.createElement('div');
        card.className = `bg-white rounded-3xl border-4 ${stn.border || 'border-slate-300'} shadow-lg overflow-hidden flex flex-col transition-card hover:shadow-xl`;
        
        card.innerHTML = `
          <!-- Station Header -->
          <div class="${stn.color || 'bg-slate-700'} text-white p-3.5 px-5 flex items-center justify-between">
            <div class="flex items-center gap-3">
              <i class="fa-solid ${stn.icon || 'fa-book-open'} text-2xl drop-shadow-sm"></i>
              <h3 class="font-extrabold text-xl tracking-wide">${stn.name}</h3>
            </div>
            <span class="bg-white/20 text-white text-xs font-bold px-2.5 py-1 rounded-full backdrop-blur-sm">
              ${assignedStudents.length} Students
            </span>
          </div>

          <!-- Station Task Description -->
          ${stn.task ? `
            <div class="bg-slate-50 border-b border-slate-100 p-2.5 px-4 flex items-center gap-2 text-xs font-semibold text-slate-600">
              <i class="fa-solid fa-circle-info text-amber-500 text-sm flex-shrink-0"></i>
              <span class="line-clamp-2">${stn.task}</span>
            </div>
          ` : ''}

          <!-- Drop Zone & Student List -->
          <div 
            class="droppable-area flex-1 p-4 min-h-[140px] flex flex-wrap gap-2.5 content-start transition-all"
            ondrop="handleDrop(event, '${stn.id}')" 
            ondragover="handleDragOver(event)"
            ondragleave="handleDragLeave(event)"
          >
            ${assignedStudents.length === 0 ? `
              <div class="w-full h-full flex flex-col items-center justify-center text-slate-300 py-6">
                <i class="fa-solid fa-user-plus text-3xl mb-1"></i>
                <span class="text-xs font-bold uppercase tracking-wider">No Students Assigned</span>
              </div>
            ` : ''}

            ${assignedStudents.map(std => {
              const grp = GROUPS.find(g => g.id === std.groupId);
              const grpClass = grp ? grp.badgeColor : 'bg-slate-200 text-slate-800';
              return `
                <div 
                  id="student-${std.id}" 
                  draggable="true" 
                  ondragstart="handleDragStart(event, '${std.id}')" 
                  class="student-badge ${grpClass} font-extrabold px-3.5 py-2 rounded-2xl shadow-sm hover:shadow-md transition-all flex items-center gap-2 group text-base"
                >
                  <i class="fa-solid fa-face-smile"></i>
                  <span>${std.name}</span>
                  <button onclick="unassignStudent('${std.id}')" class="opacity-0 group-hover:opacity-100 hover:text-red-300 ml-1 transition-opacity text-xs" title="Unassign student">
                    <i class="fa-solid fa-xmark"></i>
                  </button>
                </div>
              `;
            }).join('')}
          </div>
        `;

        container.appendChild(card);
      });

      renderRosterDrawer();
      updateStageHeader();
    }

    function renderRosterDrawer() {
      const unassigned = students.filter(s => !s.stationId || s.stationId === 'unassigned');
      document.getElementById('unassignedCount').innerText = unassigned.length;

      const unassignedBox = document.getElementById('unassignedBox');
      unassignedBox.innerHTML = unassigned.length === 0 ? `
        <span class="text-xs text-slate-400 italic p-2">All students are assigned to centers!</span>
      ` : unassigned.map(std => {
        const grp = GROUPS.find(g => g.id === std.groupId);
        const grpClass = grp ? grp.badgeColor : 'bg-slate-200 text-slate-800';
        return `
          <div 
            id="student-${std.id}" 
            draggable="true" 
            ondragstart="handleDragStart(event, '${std.id}')" 
            class="student-badge ${grpClass} font-bold px-3 py-1.5 rounded-xl text-sm flex items-center gap-2"
          >
            <i class="fa-solid fa-user"></i>
            <span>${std.name}</span>
          </div>
        `;
      }).join('');

      // Render full roster for deleting or manual station choosing
      const rosterList = document.getElementById('fullRosterList');
      rosterList.innerHTML = students.map(std => `
        <div class="flex items-center justify-between p-2 bg-white rounded-xl border border-slate-200 text-xs">
          <span class="font-bold text-slate-800">${std.name}</span>
          <div class="flex items-center gap-2">
            <select onchange="assignStudentToStation('${std.id}', this.value)" class="bg-slate-100 rounded-lg px-2 py-1 font-semibold text-slate-600 border border-slate-300">
              <option value="unassigned" ${!std.stationId || std.stationId === 'unassigned' ? 'selected' : ''}>Unassigned</option>
              ${stations.map(stn => `<option value="${stn.id}" ${std.stationId === stn.id ? 'selected' : ''}>${stn.name}</option>`).join('')}
            </select>
            <button onclick="removeStudentFromRoster('${std.id}')" class="text-rose-500 hover:text-rose-700 p-1">
              <i class="fa-solid fa-trash"></i>
            </button>
          </div>
        </div>
      `).join('');
    }

    function updateStageHeader() {
      const badge = document.getElementById('rotationStageBadge');
      const cleanUpBanner = document.getElementById('cleanUpBanner');

      if (isCleanUpPhase) {
        badge.className = "text-xs md:text-sm bg-rose-500 text-white px-3 py-1 rounded-full uppercase tracking-wider font-bold animate-pulse";
        badge.innerText = "Clean Up & Transition";
        cleanUpBanner.classList.remove('hidden');
      } else {
        badge.className = "text-xs md:text-sm bg-indigo-600 text-white px-3 py-1 rounded-full uppercase tracking-wider font-bold";
        badge.innerText = `Rotation ${rotationIndex}`;
        cleanUpBanner.classList.add('hidden');
      }
    }

    // --- DRAG AND DROP HANDLERS ---
    function handleDragStart(e, studentId) {
      draggedStudentId = studentId;
      e.dataTransfer.setData('text/plain', studentId);
      e.dataTransfer.effectAllowed = 'move';
    }

    function handleDragOver(e) {
      e.preventDefault();
      e.dataTransfer.dropEffect = 'move';
      e.currentTarget.classList.add('drag-over');
    }

    function handleDragLeave(e) {
      e.currentTarget.classList.remove('drag-over');
    }

    function handleDrop(e, targetStationId) {
      e.preventDefault();
      e.currentTarget.classList.remove('drag-over');
      if (!draggedStudentId) return;

      const std = students.find(s => s.id === draggedStudentId);
      if (std) {
        std.stationId = targetStationId === 'unassigned' ? null : targetStationId;
        saveState();
        renderBoard();
      }
      draggedStudentId = null;
    }

    // --- STUDENT & ROSTER MANAGEMENT ---
    function addNewStudentFromDrawer() {
      const input = document.getElementById('newStudentName');
      const name = input.value.trim();
      if (!name) return;

      students.push({
        id: 'std-' + Date.now(),
        name,
        groupId: 'none',
        stationId: null
      });

      input.value = '';
      saveState();
      renderBoard();
    }

    function unassignStudent(studentId) {
      const std = students.find(s => s.id === studentId);
      if (std) {
        std.stationId = null;
        saveState();
        renderBoard();
      }
    }

    function assignStudentToStation(studentId, stationId) {
      const std = students.find(s => s.id === studentId);
      if (std) {
        std.stationId = stationId === 'unassigned' ? null : stationId;
        saveState();
        renderBoard();
      }
    }

    function removeStudentFromRoster(studentId) {
      students = students.filter(s => s.id !== studentId);
      saveState();
      renderBoard();
      if (!document.getElementById('groupEditorModal').classList.contains('hidden')) {
        renderGroupEditor();
      }
    }

    function updateStudentName(studentId, newName) {
      const std = students.find(s => s.id === studentId);
      if (std && newName.trim()) {
        std.name = newName.trim();
        saveState();
        renderBoard();
      }
    }

    function updateStudentGroup(studentId, groupId) {
      const std = students.find(s => s.id === studentId);
      if (std) {
        std.groupId = groupId;
        saveState();
        renderBoard();
      }
    }

    // --- GROUP EDITOR MODAL LOGIC ---
    function toggleGroupEditorModal() {
      const modal = document.getElementById('groupEditorModal');
      const isHidden = modal.classList.contains('hidden');

      if (isHidden) {
        renderGroupEditor();
        modal.classList.remove('hidden');
      } else {
        modal.classList.add('hidden');
      }
    }

    function renderGroupEditor() {
      const container = document.getElementById('groupEditorGrid');
      
      const allGroups = [...GROUPS, { id: 'none', name: 'No Color Group Assigned', badgeColor: 'bg-slate-200 text-slate-800' }];

      container.innerHTML = allGroups.map(grp => {
        const grpStudents = students.filter(s => (grp.id === 'none' ? (!s.groupId || s.groupId === 'none') : s.groupId === grp.id));

        return `
          <div class="bg-white p-4 rounded-2xl border-2 border-slate-200 shadow-sm">
            <div class="flex items-center justify-between border-b border-slate-100 pb-2 mb-3">
              <span class="font-bold text-sm px-3 py-1 rounded-full ${grp.badgeColor}">
                ${grp.name}
              </span>
              <span class="text-xs bg-slate-100 font-bold px-2 py-0.5 rounded-full text-slate-600">
                ${grpStudents.length} Students
              </span>
            </div>

            <div class="space-y-2">
              ${grpStudents.length === 0 ? `
                <p class="text-xs text-slate-400 italic py-1">No students assigned to this group.</p>
              ` : grpStudents.map(std => `
                <div class="flex items-center gap-2 bg-slate-50 p-1.5 px-2 rounded-xl border border-slate-200">
                  <input type="text" value="${std.name}" onchange="updateStudentName('${std.id}', this.value)" class="flex-1 text-sm font-bold text-slate-800 bg-white border border-slate-200 rounded-lg px-2 py-1 focus:outline-none focus:border-emerald-500">
                  
                  <!-- Group Selector -->
                  <select onchange="updateStudentGroup('${std.id}', this.value); renderGroupEditor();" class="text-xs bg-white border border-slate-200 rounded-lg p-1 font-semibold text-slate-600">
                    <option value="grp-red" ${std.groupId === 'grp-red' ? 'selected' : ''}>🔴 Red</option>
                    <option value="grp-yellow" ${std.groupId === 'grp-yellow' ? 'selected' : ''}>🟡 Yellow</option>
                    <option value="grp-orange" ${std.groupId === 'grp-orange' ? 'selected' : ''}>🟠 Orange</option>
                    <option value="grp-purple" ${std.groupId === 'grp-purple' ? 'selected' : ''}>🟣 Purple</option>
                    <option value="grp-green" ${std.groupId === 'grp-green' ? 'selected' : ''}>🟢 Green</option>
                    <option value="none" ${!std.groupId || std.groupId === 'none' ? 'selected' : ''}>None</option>
                  </select>

                  <button onclick="removeStudentFromRoster('${std.id}')" class="text-rose-500 hover:text-rose-700 p-1 text-xs" title="Delete Student">
                    <i class="fa-solid fa-trash"></i>
                  </button>
                </div>
              `).join('')}
            </div>
          </div>
        `;
      }).join('');
    }

    function quickAddStudent() {
      const nameInput = document.getElementById('quickAddStudentName');
      const groupSelect = document.getElementById('quickAddStudentGroup');
      const name = nameInput.value.trim();

      if (!name) return;

      const grpId = groupSelect.value;
      // Auto-assign station based on group default if available
      const groupObj = GROUPS.find(g => g.id === grpId);
      const defaultStn = groupObj ? groupObj.defaultStation : null;

      students.push({
        id: 'std-' + Date.now(),
        name: name,
        groupId: grpId,
        stationId: defaultStn
      });

      nameInput.value = '';
      saveState();
      renderBoard();
      renderGroupEditor();
    }

    // --- AUTOMATIC DUAL-TIMER ROTATION LOGIC ---
    function triggerAutoRotation() {
      if (stations.length < 2) return;

      const stationIds = stations.map(s => s.id);

      // Rotate all students assigned to a station to the next station in order
      students.forEach(std => {
        if (std.stationId) {
          const currentIndex = stationIds.indexOf(std.stationId);
          if (currentIndex !== -1) {
            const nextIndex = (currentIndex + 1) % stationIds.length;
            std.stationId = stationIds[nextIndex];
          }
        }
      });

      // Advance rotation counter
      isCleanUpPhase = false;
      rotationIndex++;

      // Reset timer to 12 minutes
      timeRemainingSec = rotationDurationMins * 60;
      
      playChime('complete');
      confetti({ particleCount: 60, spread: 60, origin: { y: 0.6 } });

      if (autoStartNextRotation) {
        startTimer();
      } else {
        pauseTimer();
      }

      saveState();
      renderBoard();
      updateTimerUI();
    }

    function confirmResetDaily() {
      if (confirm("Reset back to Rotation 1 and restart the board? (Your student roster and stations will remain saved).")) {
        rotationIndex = 1;
        isCleanUpPhase = false;
        timeRemainingSec = rotationDurationMins * 60;
        pauseTimer();
        saveState();
        renderBoard();
        updateTimerUI();
      }
    }

    // --- TIMER CONTROL FUNCTIONS ---
    function toggleTimer() {
      if (isTimerRunning) {
        pauseTimer();
      } else {
        startTimer();
      }
    }

    function startTimer() {
      if (isTimerRunning) return;
      isTimerRunning = true;

      document.getElementById('startPauseBtn').className = "w-12 h-12 bg-amber-500 hover:bg-amber-600 active:scale-95 text-white rounded-xl shadow-lg flex items-center justify-center text-xl transition-all";
      document.getElementById('startPauseIcon').className = "fa-solid fa-pause";

      timerInterval = setInterval(() => {
        if (timeRemainingSec > 0) {
          timeRemainingSec--;
          updateTimerUI();
          saveState();
        } else {
          onTimerPhaseEnd();
        }
      }, 1000);
    }

    function pauseTimer() {
      isTimerRunning = false;
      clearInterval(timerInterval);

      document.getElementById('startPauseBtn').className = "w-12 h-12 bg-emerald-500 hover:bg-emerald-600 active:scale-95 text-white rounded-xl shadow-lg flex items-center justify-center text-xl transition-all";
      document.getElementById('startPauseIcon').className = "fa-solid fa-play";
    }

    function resetTimer() {
      pauseTimer();
      const maxTime = isCleanUpPhase ? cleanUpDurationMins * 60 : rotationDurationMins * 60;
      timeRemainingSec = maxTime;
      updateTimerUI();
      saveState();
    }

    function adjustTimerSeconds(amountSec) {
      timeRemainingSec = Math.max(0, timeRemainingSec + amountSec);
      updateTimerUI();
      saveState();
    }

    function skipTimerStage() {
      pauseTimer();
      onTimerPhaseEnd();
    }

    function onTimerPhaseEnd() {
      pauseTimer();

      if (!isCleanUpPhase) {
        // --- 12-min Rotation ENDS -> Switch to 2-min Transition ---
        isCleanUpPhase = true;
        timeRemainingSec = cleanUpDurationMins * 60;
        playChime('transition');
        confetti({ particleCount: 70, spread: 70, origin: { y: 0.5 } });
        startTimer(); // Auto start transition clean-up countdown
      } else {
        // --- 2-min Transition ENDS -> Automatically Rotate Student Groups ---
        triggerAutoRotation();
        return;
      }

      saveState();
      renderBoard();
      updateTimerUI();
    }

    function updateTimerUI() {
      const mins = Math.floor(timeRemainingSec / 60);
      const secs = timeRemainingSec % 60;
      const displayStr = `${mins.toString().padStart(2, '0')}:${secs.toString().padStart(2, '0')}`;
      document.getElementById('timerDisplay').innerText = displayStr;

      const totalSecs = isCleanUpPhase ? cleanUpDurationMins * 60 : rotationDurationMins * 60;
      const progressRatio = timeRemainingSec / Math.max(totalSecs, 1);
      
      const circle = document.getElementById('timerProgressCircle');
      const circumference = 163.36;
      const offset = circumference - (progressRatio * circumference);
      circle.style.strokeDashoffset = offset;

      const label = document.getElementById('timerModeLabel');
      const icon = document.getElementById('timerIcon');

      if (isCleanUpPhase) {
        label.innerText = "Clean Up Time";
        label.className = "text-xs font-bold uppercase tracking-wider text-rose-500";
        circle.className = "progress-ring__circle text-rose-500";
        icon.className = "fa-solid fa-broom text-rose-500 absolute text-lg";
      } else {
        label.innerText = `Rotation ${rotationIndex} Time`;
        label.className = "text-xs font-bold uppercase tracking-wider text-indigo-600";
        circle.className = "progress-ring__circle text-indigo-600";
        icon.className = "fa-solid fa-hourglass-half text-indigo-600 absolute text-lg";
      }
    }

    // --- DRAWER & SETTINGS MODALS ---
    function toggleRosterDrawer() {
      const drawer = document.getElementById('rosterDrawer');
      drawer.classList.toggle('translate-x-full');
    }

    function toggleEditMode() {
      const modal = document.getElementById('settingsModal');
      const isHidden = modal.classList.contains('hidden');

      if (isHidden) {
        document.getElementById('settingRotationMins').value = rotationDurationMins;
        document.getElementById('settingCleanUpMins').value = cleanUpDurationMins;
        document.getElementById('settingAutoStartNext').checked = autoStartNextRotation;
        renderSettingsStationsList();
        modal.classList.remove('hidden');
      } else {
        modal.classList.add('hidden');
      }
    }

    function renderSettingsStationsList() {
      const container = document.getElementById('settingsStationsList');
      container.innerHTML = stations.map((stn, idx) => `
        <div class="p-3 bg-slate-50 border border-slate-200 rounded-xl flex flex-col sm:flex-row items-center justify-between gap-3 text-xs">
          <div class="flex items-center gap-3 w-full sm:w-auto">
            <span class="w-6 h-6 rounded-full ${stn.color} text-white font-bold flex items-center justify-center text-xs flex-shrink-0">
              ${idx + 1}
            </span>
            <input type="text" value="${stn.name}" onchange="updateStationMeta('${stn.id}', 'name', this.value)" class="font-bold text-slate-800 bg-white border border-slate-300 rounded-lg px-2 py-1 text-sm focus:outline-none">
          </div>
          <input type="text" value="${stn.task || ''}" placeholder="Task description..." onchange="updateStationMeta('${stn.id}', 'task', this.value)" class="flex-1 w-full text-slate-600 bg-white border border-slate-300 rounded-lg px-2 py-1 text-xs focus:outline-none">
          <button onclick="deleteStation('${stn.id}')" class="text-rose-500 hover:text-rose-700 font-bold px-2 py-1">
            <i class="fa-solid fa-trash"></i>
          </button>
        </div>
      `).join('');
    }

    function updateStationMeta(id, key, val) {
      const stn = stations.find(s => s.id === id);
      if (stn) {
        stn[key] = val;
        saveState();
      }
    }

    function addNewStation() {
      const titleInput = document.getElementById('newStationTitle');
      const taskInput = document.getElementById('newStationTask');
      const iconInput = document.getElementById('newStationIcon');
      const colorInput = document.getElementById('newStationColor');

      const name = titleInput.value.trim();
      if (!name) return;

      const colorVal = colorInput.value;
      let borderVal = 'border-slate-400';
      if (colorVal.includes('red')) borderVal = 'border-red-500';
      if (colorVal.includes('amber')) borderVal = 'border-amber-400';
      if (colorVal.includes('orange')) borderVal = 'border-orange-500';
      if (colorVal.includes('purple')) borderVal = 'border-purple-600';
      if (colorVal.includes('emerald')) borderVal = 'border-emerald-500';
      if (colorVal.includes('blue')) borderVal = 'border-blue-500';

      stations.push({
        id: 'stn-' + Date.now(),
        name,
        task: taskInput.value.trim(),
        icon: iconInput.value,
        color: colorVal,
        border: borderVal
      });

      titleInput.value = '';
      taskInput.value = '';

      saveState();
      renderSettingsStationsList();
      renderBoard();
    }

    function deleteStation(id) {
      if (stations.length <= 1) {
        alert("You must keep at least one station on the board!");
        return;
      }
