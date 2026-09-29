<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Eaglercraft 1.12.2 WASM</title>
<style>
  html, body { margin:0; padding:0; width:100vw; height:100vh; overflow:hidden; background:#111318; color:#e8e8e8; font-family:system-ui,Segoe UI,Arial,sans-serif; }
  #game { position:fixed; inset:0; width:100vw; height:100vh; background:#111318; }
  #game iframe { border:0; width:100%; height:100%; display:block; background:#111318; }
  #overlay { position:fixed; inset:0; z-index:10; display:flex; align-items:center; justify-content:center; background:#111318; }
  #overlay.hidden { display:none; }
  .card { width:min(92vw,460px); background:#1b1e26; border:1px solid #2c3140; border-radius:12px; padding:22px; box-shadow:0 10px 40px rgba(0,0,0,.5); }
  h1 { font-size:20px; margin:0 0 4px; }
  p.sub { margin:0 0 16px; color:#9aa0ae; font-size:13px; }
  label { display:block; font-size:12px; color:#9aa0ae; margin:14px 0 4px; }
  input[type=text] { width:100%; box-sizing:border-box; padding:9px 10px; border-radius:8px; border:1px solid #363c4d; background:#12141a; color:#fff; font-size:13px; }
  button { margin-top:10px; width:100%; padding:11px; border:0; border-radius:8px; background:#4caf50; color:#fff; font-size:15px; font-weight:600; cursor:pointer; }
  button:hover { filter:brightness(1.1); }
  button.alt { background:#3b4256; }
  button.stealth { background:#2196F3; }
  .bar { height:14px; background:#12141a; border-radius:7px; overflow:hidden; margin-top:14px; border:1px solid #2c3140; }
  .fill { height:100%; width:0%; background:linear-gradient(90deg,#4caf50,#8bc34a); transition:width .2s; }
  #status { margin-top:10px; font-size:13px; color:#c9cdd8; min-height:18px; }
  #error { display:none; margin-top:14px; padding:12px; border-radius:8px; background:#3a1d1f; border:1px solid #7a2f33; color:#ffb4b8; font-size:13px; line-height:1.45; }
  #bar2 { position:fixed; right:8px; bottom:8px; z-index:20; display:none; gap:6px; }
  #bar2 a, #bar2 span { font-size:12px; background:#000b; color:#fff; padding:6px 10px; border-radius:6px; text-decoration:none; cursor:pointer; }
</style>
</head>
<body>
<div id="game"></div>
<div id="bar2"><span id="backBtn">⌂ Menu</span><a id="newTab" target="_blank" rel="noopener" href="#">Open in new tab ↗</a></div>

<div id="overlay">
  <div class="card">
    <h1>Eaglercraft 1.12.2</h1>
    <p class="sub">W3Schools Editor Compatible Build</p>

    <div id="setup">
      <button id="wasmBtn">Play Standard Mode</button>
      <button id="stealthBtn" class="stealth">Play in Stealth Container</button>

      <label for="customUrl">Or load any link</label>
      <input type="text" id="customUrl" placeholder="https://example.com/game/" autocomplete="off" autocapitalize="off" spellcheck="false">
      <button id="customBtn" class="alt">Load link</button>
    </div>

    <div id="progressWrap" style="display:none">
      <div class="bar"><div class="fill" id="fill"></div></div>
      <div id="status">Starting…</div>
    </div>
    <div id="error"></div>
  </div>
</div>

<script>
(function () {
  "use strict";

  var WASM_URL = "https://freedombrowser.org/static/mc/1.12.2/wasm/";
  var ALLOW = "fullscreen; pointer-lock; autoplay; clipboard-read; clipboard-write; gamepad; keyboard-map; microphone; camera; web-share; cross-origin-isolated";

  var $ = function (id) { return document.getElementById(id); };
  var game = $("game"), overlay = $("overlay"), timer = null;

  try { $("customUrl").value = localStorage.getItem("ec_url") || ""; } catch (e) {}

  function setStatus(t) { $("status").textContent = t; }
  function setProgress(p) { $("fill").style.width = p + "%"; }

  function showError(title, detail) {
    clearTimeout(timer);
    game.innerHTML = "";
    $("bar2").style.display = "none";
    overlay.classList.remove("hidden");
    $("progressWrap").style.display = "none";
    $("setup").style.display = "block";
    var el = $("error");
    el.style.display = "block";
    el.innerHTML = "<b>" + title + "</b><br>" + detail;
  }

  function webglOk() {
    try {
      var c = document.createElement("canvas");
      return !!(c.getContext("webgl2") || c.getContext("webgl"));
    } catch (e) { return false; }
  }

  function normalize(u) {
    u = (u || "").trim();
    if (!u) return "";
    if (!/^[a-z][a-z0-9+.-]*:\/\//i.test(u)) u = "https://" + u;
    return u;
  }

  function launchStealth(url) {
    if (!webglOk()) {
      showError("WebGL is unavailable", "WebGL is disabled or blocked in this browser.");
      return;
    }
    if (!/^https:\/\//i.test(url)) {
      showError("Use an https:// link", "Links must use https:// protocol.");
      return;
    }

    $("setup").style.display = "none";
    $("progressWrap").style.display = "block";
    setProgress(25); setStatus("Building stealth container…");
    game.innerHTML = "";

    var stealthHTML = '<!DOCTYPE html><html><head><meta charset="UTF-8"><title>Dashboard</title>' +
      '<style>html,body{margin:0;padding:0;width:100vw;height:100vh;overflow:hidden;background:#111318;}</style>' +
      '</head><body>' +
      '<iframe src="' + url + '" style="position:fixed;inset:0;width:100%;height:100%;border:0;" allow="' + ALLOW + '" allowfullscreen referrerpolicy="no-referrer"></iframe>' +
      '</body></html>';

    try {
      var blob = new Blob([stealthHTML], { type: 'text/html' });
      var blobUrl = URL.createObjectURL(blob);

      var f = document.createElement("iframe");
      f.title = "Game Stealth";
      f.setAttribute("allow", ALLOW);
      f.setAttribute("allowfullscreen", "");
      f.setAttribute("referrerpolicy", "no-referrer");
      
      f.addEventListener("load", function () {
        clearTimeout(timer);
        setProgress(100);
        setTimeout(function () {
          overlay.classList.add("hidden");
          $("newTab").href = url;
          $("bar2").style.display = "flex";
          try { f.focus(); } catch (e) {}
        }, 300);
      });

      game.appendChild(f);
      f.src = blobUrl;

      timer = setTimeout(function () {
        showError("The page did not load", "It may be slow, offline, or block embedding.");
      }, 20000);
    } catch (e) {
      showError("Container Error", e.message);
    }
  }

  function launch(url) {
    $("error").style.display = "none";
    if (!webglOk()) {
      showError("WebGL is unavailable", "WebGL is disabled or blocked.");
      return;
    }
    if (!/^https:\/\//i.test(url)) {
      showError("Use an https:// link", "Links must use https:// protocol.");
      return;
    }

    $("setup").style.display = "none";
    $("progressWrap").style.display = "block";
    setProgress(25); setStatus("Loading game page…");
    game.innerHTML = "";

    var f = document.createElement("iframe");
    f.title = "Game";
    f.setAttribute("allow", ALLOW);
    f.setAttribute("allowfullscreen", "");
    f.setAttribute("referrerpolicy", "no-referrer");
    f.addEventListener("load", function () {
      clearTimeout(timer);
      setProgress(100);
      setTimeout(function () {
        overlay.classList.add("hidden");
        $("newTab").href = url;
        $("bar2").style.display = "flex";
        try { f.focus(); } catch (e) {}
      }, 300);
    });
    game.appendChild(f);
    f.src = url;

    timer = setTimeout(function () {
      showError("The page did not load", "It may be slow or block embedding.");
    }, 20000);
  }

  game.addEventListener("click", function () {
    var f = game.querySelector("iframe");
    if (f) { try { f.focus(); } catch (e) {} }
  });
  window.addEventListener("keydown", function (e) {
    if (overlay.classList.contains("hidden") && (e.key === " " || e.key.indexOf("Arrow") === 0 || e.key === "Tab")) e.preventDefault();
  }, true);
  window.addEventListener("contextmenu", function (e) { if (overlay.classList.contains("hidden")) e.preventDefault(); });

  $("wasmBtn").addEventListener("click", function () { launch(WASM_URL); });
  $("stealthBtn").addEventListener("click", function () { launchStealth(WASM_URL); });

  $("customBtn").addEventListener("click", function () {
    var u = normalize($("customUrl").value);
    if (!u) { showError("No link entered", "Paste a link into the box first."); return; }
    try { localStorage.setItem("ec_url", u); } catch (e) {}
    launch(u);
  });
  $("customUrl").addEventListener("keydown", function (e) { if (e.key === "Enter") $("customBtn").click(); });
  $("backBtn").addEventListener("click", function () {
    clearTimeout(timer);
    game.innerHTML = "";
    $("bar2").style.display = "none";
    $("progressWrap").style.display = "none";
    $("setup").style.display = "block";
    overlay.classList.remove("hidden");
  });
})();
</script>
</body>
</html>
