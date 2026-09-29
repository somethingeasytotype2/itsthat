<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Canvas - Dashboard</title>
<style>
  :root {
    --canvas-red: #e03c31;
    --canvas-dark: #2d3b45;
    --canvas-bg: #f5f7f8;
    --canvas-border: #c7cdd1;
    --canvas-text: #2d3b45;
  }
  html, body { margin:0; padding:0; width:100vw; height:100vh; overflow:hidden; background:var(--canvas-bg); color:var(--canvas-text); font-family:system-ui,-apple-system,Segoe UI,Arial,sans-serif; }
  
  /* Canvas Header & Sidebar Styling */
  #app-header { position:fixed; top:0; left:0; width:100%; height:50px; background:var(--canvas-dark); color:#fff; display:flex; align-items:center; padding:0 20px; z-index:20; box-shadow:0 2px 4px rgba(0,0,0,.1); }
  #app-header .logo { font-weight:bold; font-size:18px; letter-spacing:0.5px; display:flex; align-items:center; gap:8px; }
  #app-header .logo span { color:var(--canvas-red); }
  
  #sidebar { position:fixed; top:50px; left:0; width:75px; height:calc(100vh - 50px); background:#1a2329; display:flex; flex-direction:column; align-items:center; padding-top:15px; gap:20px; z-index:20; border-right:1px solid #33404a; }
  .nav-item { color:#a5b1b8; font-size:11px; text-align:center; text-decoration:none; cursor:pointer; display:flex; flex-direction:column; align-items:center; gap:4px; }
  .nav-item.active { color:#fff; }
  .nav-icon { width:24px; height:24px; background:#33404a; border-radius:4px; display:flex; align-items:center; justify-content:center; font-weight:bold; font-size:12px; }
  .nav-item.active .nav-icon { background:var(--canvas-red); }

  /* Main Dashboard Content */
  #main-content { position:fixed; top:50px; left:75px; right:0; bottom:0; padding:30px; overflow-y:auto; display:block; z-index:10; background:var(--canvas-bg); }
  h1 { font-size:24px; margin:0 0 20px; color:var(--canvas-dark); font-weight:600; }
  
  .dashboard-grid { display:grid; grid-template-columns:repeat(auto-fill, minmax(280px, 1fr)); gap:20px; max-width:1000px; }
  .course-card { background:#fff; border:1px solid var(--canvas-border); border-radius:8px; overflow:hidden; box-shadow:0 1px 3px rgba(0,0,0,.05); transition:transform .15s, box-shadow .15s; }
  .course-card:hover { transform:translateY(-2px); box-shadow:0 4px 10px rgba(0,0,0,.08); }
  .course-header { height:90px; background:linear-gradient(135deg, #2d3b45, #415462); padding:15px; color:#fff; display:flex; flex-direction:column; justify-content:flex-end; }
  .course-title { font-weight:600; font-size:15px; margin:0; }
  .course-code { font-size:12px; opacity:0.8; margin-top:2px; }
  .course-body { padding:15px; font-size:13px; color:#555; }
  .course-btn { display:block; width:100%; box-sizing:border-box; text-align:center; padding:9px; background:var(--canvas-red); color:#fff; border-radius:4px; text-decoration:none; font-weight:600; margin-top:12px; cursor:pointer; border:0; }
  .course-btn:hover { background:#c6342b; }

  /* Game / Sandbox Fullscreen Frame Container */
  #game-container { position:fixed; inset:0; top:50px; left:75px; width:calc(100vw - 75px); height:calc(100vh - 50px); background:#000; z-index:30; display:none; }
  #game-container iframe { width:100%; height:100%; border:0; display:block; }
  
  /* Floating exit menu bar inside game */
  #bar2 { position:fixed; right:15px; bottom:15px; z-index:40; display:none; gap:6px; }
  #bar2 span { font-size:12px; background:rgba(0,0,0,0.8); color:#fff; padding:6px 12px; border-radius:4px; text-decoration:none; cursor:pointer; font-weight:500; }
  #bar2 span:hover { background:rgba(0,0,0,0.95); }
</style>
</head>
<body>

<!-- Canvas Top Navigation Header -->
<div id="app-header">
  <div class="logo"><span>Canvas</span> LMS</div>
</div>

<!-- Canvas Left Sidebar -->
<div id="sidebar">
  <div class="nav-item active">
    <div class="nav-icon">🏠</div>
    Dashboard
  </div>
  <div class="nav-item">
    <div class="nav-icon">📚</div>
    Courses
  </div>
  <div class="nav-item">
    <div class="nav-icon">📅</div>
    Calendar
  </div>
</div>

<!-- Dashboard Home View -->
<div id="main-content">
  <h1>My Dashboard</h1>
  <div class="dashboard-grid">
    
    <!-- Decoy Course Card 1 -->
    <div class="course-card">
      <div class="course-header" style="background:linear-gradient(135deg, #1f4068, #162447);">
        <p class="course-title">AP Computer Science Principles</p>
        <p class="course-code">CS101 - Fall 2026</p>
      </div>
      <div class="course-body">
        <p style="margin:0 0 10px;">Module 4: WebAssembly & 3D Interactive Environments Lab.</p>
        <button class="course-btn" id="launchBtn">Open Module</button>
      </div>
    </div>

    <!-- Decoy Course Card 2 -->
    <div class="course-card">
      <div class="course-header" style="background:linear-gradient(135deg, #28527a, #8f43ee);">
        <p class="course-title">Advanced Mathematics</p>
        <p class="course-code">MATH302 - Period 3</p>
      </div>
      <div class="course-body">
        <p style="margin:0 0 10px;">Calculus graphing calculator and vector matrix worksheets.</p>
        <button class="course-btn" style="background:#555; cursor:default;" onclick="alert('Module locked by instructor.')">Locked</button>
      </div>
    </div>

  </div>
</div>

<!-- Active Simulation Container -->
<div id="game-container"></div>
<div id="bar2"><span id="backBtn">← Return to Dashboard</span></div>

<script>
(function () {
  "use strict";

  var TARGET_URL = "https://alexander-datskov.github.io/1.12-eaglercraftx/";
  var ALLOW = "fullscreen; pointer-lock; autoplay; clipboard-read; clipboard-write; gamepad; keyboard-map; microphone; camera; web-share; cross-origin-isolated";

  var $ = function (id) { return document.getElementById(id); };

  $("launchBtn").addEventListener("click", function () {
    $("main-content").style.display = "none";
    var container = $("game-container");
    container.style.display = "block";
    container.innerHTML = "";

    // Generate isolated container payload to protect against embedding restrictions
    var stealthHTML = '<!DOCTYPE html><html><head><meta charset="UTF-8"><title>Module Session</title>' +
      '<style>html,body{margin:0;padding:0;width:100vw;height:100vh;overflow:hidden;background:#000;}</style>' +
      '</head><body>' +
      '<iframe src="' + TARGET_URL + '" style="position:fixed;inset:0;width:100%;height:100%;border:0;" allow="' + ALLOW + '" allowfullscreen referrerpolicy="no-referrer"></iframe>' +
      '</body></html>';

    try {
      var blob = new Blob([stealthHTML], { type: 'text/html' });
      var blobUrl = URL.createObjectURL(blob);

      var f = document.createElement("iframe");
      f.title = "Simulation";
      f.setAttribute("allow", ALLOW);
      f.setAttribute("allowfullscreen", "");
      f.setAttribute("referrerpolicy", "no-referrer");

      container.appendChild(f);
      f.src = blobUrl;
      $("bar2").style.display = "flex";

      try { f.focus(); } catch (e) {}
    } catch (e) {
      alert("Failed to initialize session container.");
    }
  });

  $("backBtn").addEventListener("click", function () {
    var container = $("game-container");
    container.innerHTML = "";
    container.style.display = "none";
    $("bar2").style.display = "none";
    $("main-content").style.display = "block";
  });
})();
</script>
</body>
</html>
