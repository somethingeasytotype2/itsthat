<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8">
<title>Minicraft — 1.8.9-inspired</title>
<meta name="viewport" content="width=device-width,initial-scale=1,viewport-fit=cover">
<style>
html,body{margin:0;width:100%;height:100%;overflow:hidden;background:#79b7e5;font-family:"Courier New",monospace;user-select:none;-webkit-user-select:none}
#gl{position:fixed;inset:0;width:100%;height:100%;display:block;image-rendering:pixelated;background:#79b7e5;touch-action:none}
#hud{position:fixed;inset:0;pointer-events:none;display:none;color:#fff;text-shadow:1px 1px #000,0 0 2px #000}
#cross{position:absolute;left:50%;top:50%;width:18px;height:18px;margin:-9px 0 0 -9px;mix-blend-mode:difference}
#cross:before,#cross:after{content:"";position:absolute;background:#fff}
#cross:before{left:8px;top:0;width:2px;height:18px}
#cross:after{top:8px;left:0;height:2px;width:18px}
#info{position:absolute;left:8px;top:8px;font-size:13px;line-height:1.25;white-space:pre}
#bname{position:absolute;left:50%;bottom:122px;transform:translateX(-50%);font-size:15px;min-height:18px}
#toast{position:absolute;left:50%;top:70%;transform:translateX(-50%);font-size:16px;opacity:0;transition:opacity .15s;white-space:nowrap}
#mineW{position:absolute;left:50%;top:calc(50% + 24px);width:66px;height:6px;margin-left:-33px;border:1px solid #000;background:#0008;opacity:0}
#mine{height:100%;width:0;background:#eee}
#flash{position:absolute;inset:0;background:#f22;opacity:0}
#vignette{position:absolute;inset:0;background:radial-gradient(ellipse at center,transparent 45%,rgba(0,0,0,.32) 100%);opacity:.15}
#stats{position:absolute;left:50%;bottom:72px;transform:translateX(-50%);width:min(480px,88vw);height:46px}
#bar{position:absolute;left:50%;bottom:8px;transform:translateX(-50%);display:flex;gap:3px;padding:3px;background:rgba(0,0,0,.55);border:3px solid #1d1d1d;box-shadow:inset 2px 2px #575757,inset -2px -2px #090909}
.slot{position:relative;width:44px;height:44px;box-sizing:border-box;margin:0;border:3px solid #555;background:rgba(120,120,120,.55);display:flex;align-items:center;justify-content:center}
.slot.sel{border-color:#fff;background:rgba(255,255,255,.18);box-shadow:inset 2px 2px #ddd,inset -2px -2px #333}
.slot canvas{width:32px;height:32px;image-rendering:pixelated}
.slot i{position:absolute;right:2px;bottom:0;font:bold 12px monospace;color:#fff;font-style:normal;text-shadow:1px 1px #000}
.slot b{position:absolute;left:2px;top:-1px;font:bold 9px monospace;color:#ddd}
#hand{position:absolute;right:4%;bottom:-24px;width:140px;height:140px;image-rendering:pixelated;transform:rotate(-10deg);transform-origin:82% 100%;filter:drop-shadow(4px 4px 0 #0005)}
#hand.sw{animation:sw .25s}
@keyframes sw{0%{transform:rotate(-10deg)}40%{transform:rotate(-65deg) translate(-20px,-26px)}100%{transform:rotate(-10deg)}}
#menu,#panelShade{position:fixed;inset:0}
#menu{display:flex;flex-direction:column;align-items:center;justify-content:center;color:#fff;text-align:center;background:#151515;overflow:auto}
.menuCard{width:min(760px,92vw);padding:28px 22px 26px;box-sizing:border-box;background:linear-gradient(#3c3c3c,#222);border:4px solid #050505;box-shadow:inset 3px 3px #858585,inset -3px -3px #090909,0 16px 50px #0008}
#menu h1{margin:0;font-size:clamp(42px,9vw,96px);letter-spacing:.08em;color:#c9c9c9;text-shadow:4px 4px #3a3a3a,8px 8px #131313;-webkit-text-stroke:2px #2b2b2b}
.sub{margin:7px 0 22px;font-size:18px;color:#ffff55;text-shadow:2px 2px #3f3f15;animation:pulse 1.1s ease-in-out infinite alternate}
@keyframes pulse{from{transform:rotate(-2deg) scale(1)}to{transform:rotate(-2deg) scale(1.05)}}
.btnrow{display:flex;flex-wrap:wrap;gap:10px;justify-content:center}
button{font:bold 18px "Courier New",monospace;color:#e7e7e7;background:#737373;border:3px solid #000;box-shadow:inset -3px -3px #3b3b3b,inset 3px 3px #aaa;padding:11px 26px;cursor:pointer;text-shadow:2px 2px #333}
button:hover:not(:disabled){background:#7c8fd1;color:#fff8a0}
button:disabled{color:#999;cursor:default}
#msg{min-height:22px;margin:10px 0;font-size:14px}
.help{background:#0009;border:2px solid #000;padding:11px 15px;margin-top:16px;font-size:13px;line-height:1.65;text-align:left}
#panelShade{display:none;align-items:center;justify-content:center;background:rgba(0,0,0,.56);pointer-events:auto}
.panel{background:#c6c6c6;border:4px solid #000;box-shadow:inset 3px 3px #fff,inset -3px -3px #555;padding:14px;color:#202020;min-width:min(410px,92vw);box-sizing:border-box}
.panel h2{margin:0 0 10px;text-align:center;font-size:20px;text-shadow:none}
.panel h3{margin:0 0 6px;font-size:13px}
.panelGrid{display:grid;gap:2px}
.twoCol{display:grid;grid-template-columns:1fr 1fr;gap:14px}
.craftRow{display:flex;align-items:center;justify-content:center;gap:12px;margin-bottom:13px}
.craftArrow{font-size:26px}
#craftGrid{grid-template-columns:repeat(3,44px)}
#furnaceUI .slot,#chestUI .slot,#invUI .slot{cursor:pointer}
#furnaceUI{display:none}
.smeltBox{display:flex;align-items:center;justify-content:center;gap:8px;margin:8px 0 12px}
.arrowBox{width:46px;text-align:center;font-size:24px}
.progress{width:76px;height:8px;border:2px solid #333;background:#777}
.progress>div{height:100%;width:0;background:#eee}
#invUI{display:flex;flex-direction:column;gap:7px}
.invRows{display:grid;grid-template-columns:repeat(9,44px);gap:2px}
.invHelp{font-size:11px;line-height:1.5;margin:3px 0 0}
#death{display:none;position:fixed;inset:0;align-items:center;justify-content:center;flex-direction:column;background:rgba(105,0,0,.83);color:#fff;gap:11px;text-align:center}
.deathTitle{font-size:58px;text-shadow:4px 4px #2c0000;margin:0}
.deathSub{font-size:16px}
#pause{display:none;position:fixed;inset:0;align-items:center;justify-content:center;background:rgba(0,0,0,.5);color:#fff;pointer-events:auto}
.pauseCard{min-width:min(420px,86vw);padding:24px;background:#333;border:4px solid #000;box-shadow:inset 3px 3px #888,inset -3px -3px #111;text-align:center}
.pauseCard h2{font-size:34px;margin:0 0 18px}
.pauseCard .btnrow{display:flex;flex-direction:column}
#f1{position:fixed;right:9px;bottom:10px;font-size:10px;color:#ddd;opacity:.7;pointer-events:none}
</style>
</head>
<body>
<canvas id="gl"></canvas>

<div id="hud">
  <div id="cross"></div>
  <div id="info"></div>
  <div id="bname"></div>
  <div id="toast"></div>
  <canvas id="stats" width="480" height="46"></canvas>
  <div id="bar"></div>
  <canvas id="hand" width="16" height="16"></canvas>
  <div id="mineW"><div id="mine"></div></div>
  <div id="flash"></div>
  <div id="vignette"></div>
  <div id="f1">F1 HUD • F3 debug • F5 save • F9 load • F11 fullscreen</div>
</div>

<div id="menu">
  <div class="menuCard">
    <h1>MINICRAFT</h1>
    <div class="sub" id="subtitle">A tiny block world with big ambitions</div>
    <div class="btnrow">
      <button id="play">Play World</button>
      <button id="newGame">New World</button>
    </div>
    <div id="msg"></div>
    <div class="help">
      <b>Movement</b> — WASD move • Space jump • Shift sprint • Mouse look • Arrow keys also look<br>
      <b>Blocks</b> — LMB mine/attack • RMB place/use • 1–9 / wheel hotbar • Q drop one<br>
      <b>Interface</b> — E inventory • Esc pause/close • F5 save • F9 load • F11 fullscreen • F3 debug • F1 hide HUD<br>
      <b>Survival</b> — gather wood, craft tools, mine ores, smelt, eat, fight at night, sleep in a bed, explore the biomes
    </div>
  </div>
</div>

<div id="pause">
  <div class="pauseCard">
    <h2>Game Paused</h2>
    <div class="btnrow">
      <button id="resumeBtn">Resume</button>
      <button id="saveBtn">Save World</button>
      <button id="menuBtn">Exit to Menu</button>
    </div>
  </div>
</div>

<div id="panelShade">
  <div class="panel" id="invUI">
    <h2>Inventory</h2>
    <div class="craftRow">
      <div>
        <h3>Crafting</h3>
        <div id="craftGrid" class="panelGrid"></div>
      </div>
      <div class="craftArrow">→</div>
      <div>
        <h3>Output</h3>
        <div id="craftOut" class="panelGrid"></div>
      </div>
    </div>
    <h3>Inventory</h3>
    <div id="invMain" class="invRows"></div>
    <h3>Hotbar</h3>
    <div id="invHot" class="invRows"></div>
    <div class="invHelp">
      Right-click splits a stack or places one. Left-click moves/stacks.
      Right-click food/equipment/placeables in the world. Crafting table opens a 3×3 grid.
    </div>
  </div>

  <div class="panel" id="furnaceUI">
    <h2>Furnace</h2>
    <div class="smeltBox">
      <div>
        <h3>Input</h3>
        <div id="fIn"></div>
      </div>
      <div class="arrowBox">→</div>
      <div>
        <h3>Output</h3>
        <div id="fOut"></div>
      </div>
    </div>
    <div style="display:flex;justify-content:center">
      <div>
        <h3>Fuel</h3>
        <div id="fFuel"></div>
      </div>
    </div>
    <div class="progress"><div id="smeltProgress"></div></div>
  </div>

  <div class="panel" id="chestUI">
    <h2>Chest</h2>
    <div id="chestGrid" class="invRows"></div>
    <h3 style="margin-top:12px">Inventory</h3>
    <div id="chestInv" class="invRows"></div>
    <h3>Hotbar</h3>
    <div id="chestHot" class="invRows"></div>
  </div>
</div>

<div id="death">
  <div class="deathTitle">You died!</div>
  <div class="deathSub" id="deathSub"></div>
  <div class="btnrow">
    <button id="respawn">Respawn</button>
    <button id="deathMenu">Main Menu</button>
  </div>
</div>

<script>
(function(){
'use strict';

var $=function(id){return document.getElementById(id)};
var cv=$('gl'),gl=null;

var CS=16,H=96,SEA=30,VERSION=8;
var SAVEKEY='minicraft_189_infinite_v8';
var VIEW=6,CACHE=8;

var SEED=(Math.random()*2147483647)|0;
var menu=$('menu'),hud=$('hud'),msg=$('msg'),playBtn=$('play');
var errShown=false;

function fail(t){
  msg.textContent=t;
  msg.style.color='#ff8888';
  playBtn.disabled=true;
  playBtn.textContent='Unavailable';
}

/* ---------- pixel atlas ---------- */

var atlas=document.createElement('canvas');
atlas.width=atlas.height=256;

var actx=atlas.getContext('2d');
var img=actx.createImageData(256,256);
var rs=1337;

function rnd(){
  rs=(Math.imul(rs,1664525)+1013904223)>>>0;
  return rs/4294967296;
}

function px(r,g,b,a){
  var k=(rnd()-.5)*a;
  return [
    Math.max(0,Math.min(255,r+k)),
    Math.max(0,Math.min(255,g+k)),
    Math.max(0,Math.min(255,b+k)),
    255
  ];
}

function tile(n,fn){
  var ox=(n%16)*16;
  var oy=((n/16)|0)*16;

  for(var y=0;y<16;y++){
    for(var x=0;x<16;x++){
      var c=fn(x,y);
      var i=((oy+y)*256+ox+x)*4;

      img.data[i]=c[0];
      img.data[i+1]=c[1];
      img.data[i+2]=c[2];
      img.data[i+3]=c.length>3?c[3]:255;
    }
  }
}

function solidTile(n,r,g,b,a){
  tile(n,function(){
    return px(r,g,b,a);
  });
}

solidTile(0,95,157,58,28);
tile(1,function(x,y){
  return y<4?px(95,157,58,24):px(133,96,67,22);
});
solidTile(2,133,96,67,22);
solidTile(3,128,128,128,34);

tile(4,function(x,y){
  return x%5===0?px(72,50,28,14):px(111,82,45,20);
});

tile(5,function(x,y){
  return ((x-7.5)*(x-7.5)+(y-7.5)*(y-7.5)<58)
    ?px(186,151,94,16)
    :px(126,89,51,10);
});

solidTile(6,48,125,40,36);
solidTile(7,175,140,84,12);
solidTile(8,70,70,70,16);
solidTile(9,219,204,126,16);
solidTile(10,156,123,74,12);
solidTile(11,170,165,155,13);
solidTile(12,132,132,132,16);
solidTile(13,169,131,72,14);
solidTile(14,80,118,55,10);
solidTile(15,142,142,142,28);
solidTile(16,175,170,159,22);
solidTile(17,219,219,219,12);
solidTile(18,76,76,76,48);
solidTile(19,133,133,133,30);
solidTile(20,128,128,128,30);
solidTile(21,120,120,120,30);
solidTile(22,122,122,122,30);

tile(23,function(x,y){
  return x===7||x===8||y===7||y===8
    ?px(255,224,95,14)
    :px(126,82,42,16);
});

tile(24,function(){
  return [95,177,223,160];
});

tile(25,function(x,y){
  return (x===0||y===0||x===15||y===15)
    ?[245,250,255,135]
    :[195,220,238,78];
});

tile(26,function(x,y){
  return ((x+y)/4|0)%2
    ?px(118,149,84,18)
    :px(91,132,67,15);
});

tile(27,function(x,y){
  return (x>=4&&x<=11&&y>=4&&y<=12)
    ?px(236,72,86,20)
    :[0,0,0,0];
});

tile(28,function(x,y){
  var d=(x-7.5)*(x-7.5)+(y-7.5)*(y-7.5);
  return d<28?px(100,68,48,18):[0,0,0,0];
});

tile(29,function(x,y){
  return (x>=6&&x<=9&&y>=5&&y<=13)
    ?px(99,73,38,16)
    :[0,0,0,0];
});

solidTile(30,210,210,210,10);
solidTile(31,198,198,205,10);
solidTile(32,157,52,39,14);
solidTile(33,235,151,68,14);
solidTile(34,205,205,210,14);
solidTile(35,235,195,76,14);
solidTile(36,68,205,220,15);
solidTile(37,205,36,36,16);
solidTile(38,35,35,38,14);
solidTile(39,165,122,74,12);

function toolArt(n,r,g,b){
  tile(n,function(x,y){
    var head=(y<7&&x>3&&x<13)||(x>6&&x<10&&y<11);
    var shaft=x>=7&&x<=8&&y>=5&&y<=14;
    return head||shaft?px(r,g,b,18):[0,0,0,0];
  });
}

toolArt(40,171,135,81);
toolArt(41,130,130,130);
toolArt(42,211,211,216);
toolArt(43,171,135,81);
toolArt(44,130,130,130);
toolArt(45,211,211,216);
toolArt(46,171,135,81);
toolArt(47,130,130,130);
toolArt(48,211,211,216);
toolArt(49,171,135,81);
toolArt(50,130,130,130);
toolArt(51,211,211,216);

tile(52,function(x,y){
  return (x===4||x===11||y===4||y===11)
    ?px(210,210,215,14)
    :[0,0,0,0];
});

tile(54,function(x,y){
  return y>=5&&y<=10&&x>=2&&x<=13
    ?px(170,60,50,20)
    :[0,0,0,0];
});

tile(55,function(x,y){
  return x>=5&&x<=10&&y>=4&&y<=13
    ?px(245,245,245,18)
    :[0,0,0,0];
});

tile(56,function(x,y){
  return y===8||x===8
    ?px(255,235,110,14)
    :[0,0,0,0];
});

solidTile(57,235,235,235,0);

tile(58,function(x,y){
  var dx=x-7.5,dy=y-7.5;
  return dx*dx+dy*dy<60?[248,222,84,255]:[0,0,0,0];
});

solidTile(59,218,221,230,0);
solidTile(60,255,255,255,0);

/* Entity textures */

tile(64,function(x,y){
  var patch=(x<5&&y>7)||(x>9&&y<5)||((x+y)%11===0);
  return patch?px(106,65,34,18):px(145,99,52,16);
});

solidTile(65,150,105,63,8);
solidTile(66,61,38,21,8);

tile(67,function(x,y){
  var patch=(x<4&&y<5)||(x>10&&y>8)||((x*3+y)%13===0);
  return patch?px(200,118,121,14):px(224,143,151,12);
});

tile(68,function(x,y){
  return (x<3||x>12||y<3||y>12)
    ?px(178,100,103,12)
    :px(238,161,170,12);
});

tile(69,function(x,y){
  return ((x+y)%5===0||x===2||x===13)
    ?px(197,197,191,10)
    :px(228,228,220,11);
});

tile(70,function(x,y){
  return ((x-7)*(x-7)+(y-7)*(y-7)<60)
    ?px(242,242,237,8)
    :[0,0,0,0];
});

solidTile(71,230,110,35,8);

tile(72,function(x,y){
  return (x<3||x>12||y<3||y>12)
    ?px(43,93,137,14)
    :px(64,121,177,12);
});

tile(73,function(x,y){
  var flesh=x>3&&x<12&&y>4&&y<13;
  return flesh?px(103,71,48,14):px(74,150,102,12);
});

tile(74,function(x,y){
  return ((x+y)%7===0)
    ?px(182,182,177,10)
    :px(218,218,213,10);
});

solidTile(75,67,147,60,12);

tile(76,function(x,y){
  return (x<3||y<3||x>12||y>12)
    ?px(40,37,36,16)
    :px(68,58,54,14);
});

solidTile(77,18,18,18,5);
solidTile(78,160,36,42,5);
solidTile(79,245,245,245,0);
solidTile(80,47,47,42,9);
solidTile(81,125,82,48,10);
solidTile(82,156,156,156,8);

/* Foliage / bark */

tile(160,function(x,y){
  return (x===0||y===0||x===15||y===15)
    ?px(66,118,49,12)
    :px(74+(x+y)%4*3,140+(x%3)*4,53+(y%4)*3,12);
});

tile(161,function(x,y){
  return (x+y)%6===0
    ?px(59,111,43,10)
    :px(72,132,48,12);
});

tile(162,function(x,y){
  var hole=((x*7+y*11)%17===0);
  return hole?[0,0,0,0]:px(68+(x%4)*3,136+(y%3)*4,48+(x+y)%4*2,13);
});

tile(163,function(x,y){
  var c=(x-7.5)*(x-7.5)+(y-7.5)*(y-7.5);
  return c<47?px(164,119,68,10):px(113,78,40,10);
});

tile(164,function(x,y){
  return (x%4===0||y%4===0)
    ?px(95,63,32,10)
    :px(137,99,54,10);
});

tile(165,function(x,y){
  var ring=x===7||x===8||y===7||y===8;
  return ring?px(196,151,91,10):px(154,111,60,10);
});

/* Entity face variants */

function varTile(n,base,shade){
  tile(n,function(x,y){
    var edge=x===0||y===0||x===15||y===15;
    return px(
      Math.max(0,base[0]+shade+(edge?-6:0)),
      Math.max(0,base[1]+shade+(edge?-6:0)),
      Math.max(0,base[2]+shade+(edge?-6:0)),
      6
    );
  });
}

var mats={
96:[144,98,52],97:[132,84,40],98:[155,106,55],99:[126,78,39],
100:[166,116,62],101:[224,142,151],102:[210,127,137],103:[239,156,167],
104:[198,113,124],105:[230,147,158],106:[238,160,170],
107:[229,229,221],108:[210,210,204],109:[236,236,228],
110:[202,202,198],111:[240,240,232],112:[245,245,239],113:[229,229,223],
114:[238,238,232],115:[232,111,35],116:[48,112,165],117:[61,125,178],
118:[57,104,150],119:[68,134,184],120:[41,91,135],
121:[96,67,46],122:[111,78,52],123:[88,59,42],124:[108,79,53],
125:[79,56,43],126:[228,228,222],127:[205,205,199],128:[236,236,230],
129:[194,194,188],130:[218,218,212],131:[70,150,61],132:[59,134,53],
133:[83,165,69],134:[51,128,46],135:[74,158,60],136:[59,53,50],
137:[72,63,58],138:[49,44,42],139:[67,59,54],140:[56,49,46]
};

Object.keys(mats).forEach(function(k){
  varTile(+k,mats[k],((+k)%6)*2-5);
});

/* Utility block textures */

solidTile(150,205,177,104,4);
solidTile(151,173,138,82,4);
solidTile(152,142,104,57,6);
solidTile(153,195,160,94,6);
solidTile(154,105,105,105,0);

tile(155,function(x,y){
  return (x>=4&&x<=11&&y>=4&&y<=11)
    ?px(224,224,224,4)
    :px(75,75,75,4);
});

solidTile(156,151,90,47,4);
solidTile(157,177,109,55,4);
solidTile(158,235,191,50,0);

actx.putImageData(img,0,0);

/* ---------- block/item data ---------- */

var B=[];

function bd(id,name,top,bot,side,o){
  B[id]=Object.assign({
    id:id,
    name:name,
    top:top,
    bot:bot,
    side:side,
    solid:true,
    transparent:false,
    cutout:false,
    blend:false,
    hard:1,
    tool:null,
    harvest:0,
    drop:id,
    light:0,
    shape:'cube'
  },o||{});
}

bd(0,'Air',0,0,0,{solid:false,transparent:true,hard:0,drop:0});
bd(1,'Grass',0,2,1,{hard:.6,tool:'shovel',drop:2});
bd(2,'Dirt',2,2,2,{hard:.5,tool:'shovel'});
bd(3,'Stone',3,3,3,{
  hard:1.5,
  tool:'pickaxe',
  harvest:0,
  requiresToolDrop:true,
  drop:8
});
bd(4,'Oak Log',5,5,4,{hard:2,tool:'axe'});
bd(5,'Oak Leaves',160,161,162,{
  hard:.2,
  tool:'shears',
  drop:5,
  transparent:true,
  cutout:true
});
bd(6,'Oak Planks',7,7,7,{hard:2,tool:'axe'});
bd(7,'Bedrock',8,8,8,{
  hard:999,
  tool:'pickaxe',
  harvest:99,
  drop:0
});
bd(8,'Cobblestone',8,8,8,{
  hard:2,
  tool:'pickaxe',
  harvest:0,
  requiresToolDrop:true
});
bd(9,'Sand',9,9,9,{hard:.5,tool:'shovel'});
bd(10,'Sandstone',10,10,10,{
  hard:2,
  tool:'pickaxe',
  harvest:0,
  requiresToolDrop:true
});
bd(13,'Crafting Table',7,7,7,{
  hard:2.5,
  tool:'axe',
  shape:'table'
});
bd(14,'Coal Ore',18,18,18,{
  hard:3,
  tool:'pickaxe',
  harvest:0,
  requiresToolDrop:true,
  drop:112
});
bd(15,'Iron Ore',19,19,19,{
  hard:3,
  tool:'pickaxe',
  harvest:1,
  requiresToolDrop:true,
  drop:114
});
bd(16,'Gold Ore',20,20,20,{
  hard:3,
  tool:'pickaxe',
  harvest:2,
  requiresToolDrop:true,
  drop:138
});
bd(17,'Diamond Ore',21,21,21,{
  hard:3,
  tool:'pickaxe',
  harvest:2,
  requiresToolDrop:true,
  drop:117
});
bd(18,'Redstone Ore',22,22,22,{
  hard:3,
  tool:'pickaxe',
  harvest:2,
  requiresToolDrop:true,
  drop:118
});
bd(19,'Furnace',12,12,12,{
  hard:3.5,
  tool:'pickaxe',
  harvest:0,
  requiresToolDrop:true,
  shape:'furnace'
});
bd(25,'Water',24,24,24,{
  solid:false,
  transparent:true,
  blend:true,
  hard:.1,
  drop:0,
  shape:'liquid'
});
bd(26,'Glass',25,25,25,{
  solid:true,
  transparent:true,
  blend:true,
  hard:.3,
  tool:'pickaxe',
  drop:0
});
bd(27,'Torch',23,23,23,{
  solid:false,
  transparent:true,
  cutout:true,
  hard:.1,
  drop:27,
  light:14,
  shape:'torch'
});
bd(28,'Mossy Cobblestone',14,14,14,{
  hard:2,
  tool:'pickaxe'
});
bd(29,'Gravel',15,15,15,{hard:.6,tool:'shovel'});
bd(30,'Clay',16,16,16,{hard:.6,tool:'shovel',drop:113});
bd(31,'Snow Block',17,17,17,{hard:.2,tool:'shovel'});
bd(32,'Chest',13,13,13,{
  hard:2.5,
  tool:'axe',
  shape:'chest'
});

/*
 * Important mining rule:
 * Every normal block can be broken without its preferred tool.
 * The preferred tool only changes mining speed.
 * Blocks marked requiresToolDrop still require the appropriate
 * tool for the normal item drop, matching Minecraft behavior.
 */

function toolPower(it,def){
  var t=toolInfo(it);

  if(!t){
    return def&&def.tool?0.2:1;
  }

  if(t.tool==='shears'&&def.tool==='shears'){
    return 8;
  }

  if(def.tool===t.tool){
    return t.mat===0?3.5:t.mat===1?6:9;
  }

  return def&&def.tool?0.2:1;
}

function effectiveTool(it,def){
  var t=toolInfo(it);
  return !!(t&&def&&def.tool&&t.tool===def.tool);
}

function breakBlock(){
  if(!T)return;

  var id=get(T.x,T.y,T.z);
  var def=B[id];
  var it=inv[cur];

  if(id===0||id===7)return;

  var effective=effectiveTool(it,def);
  var drop=def.drop||id;

  /*
   * You may always break the block.
   * If the block requires a tool for its normal harvest,
   * breaking it without that tool simply gives no normal drop.
   */
  if(def.requiresToolDrop){
    var t=toolInfo(it);

    if(!t||t.tool!==def.tool||t.mat<def.harvest){
      drop=0;
    }
  }

  if(id===5&&t){
    drop=5;
  }else if(id===5&&Math.random()<.08){
    drop=135;
  }else if(id===9&&Math.random()<.12){
    drop=113;
  }

  setB(T.x,T.y,T.z,0);
  burst(T.x,T.y,T.z,id);

  if(effective||!def.tool||id===5){
    damageTool(cur,1);
  }

  if(drop){
    addItem(drop,1);
  }

  snd(95,.07,'square');

  if(id>=14&&id<=18){
    addXP(id===17?5:id===16?3:id===15?2:1);
  }
}

function mine(dt){
  if(!T){
    mineP=0;
    return;
  }

  var id=get(T.x,T.y,T.z);
  var key=T.x+','+T.y+','+T.z;

  if(key!==mineK){
    mineK=key;
    mineP=0;
  }

  if(!id||id===7){
    mineP=0;
    return;
  }

  var def=B[id];
  var speed=toolPower(inv[cur],def)/(def.hard||1);

  mineP+=dt*speed;

  if(mineP>=1){
    mineP=0;
    breakBlock();
  }
}

/* ---------- rest of game engine ---------- */

function tryUse(){
  var it=inv[cur];

  if(T){
    var tid=get(T.x,T.y,T.z);

    if(tid===13){
      openPanel('inv');
      craft3=true;
      renderCraftGrid();
      return;
    }

    if(tid===19){
      openPanel('furnace');
      return;
    }

    if(tid===32){
      openPanel('chest',T.x+','+T.y+','+T.z);
      return;
    }

    if(tid===36){
      if(L>.48){
        say('You can only sleep at night');
        return;
      }

      tod=.22;
      say('Sweet dreams.');
      return;
    }
  }

  if(it&&ITEMS[it.id]?.food){
    if(food<20){
      food=Math.min(20,food+ITEMS[it.id].food);
      sat=Math.min(food,sat+3);
      removeFromSlot(cur,1);
      snd(320,.13,'triangle');
      say('Yum!');
    }
    return;
  }

  if(it&&it.id===137){
    armor=6;
    removeFromSlot(cur,1);
    say('Iron chestplate equipped');
    return;
  }

  if(!T||!it||!ITEMS[it.id]?.place)return;

  var x=T.x+T.nx;
  var y=T.y+T.ny;
  var z=T.z+T.nz;

  if(get(x,y,z)!==0)return;

  if(P.x-HW<x+1&&P.x+HW>x&&P.y<y+1&&P.y+PH>y&&P.z-HW<z+1&&P.z+HW>z)return;

  setB(x,y,z,ITEMS[it.id].place);
  removeFromSlot(cur,1);
  snd(160,.08,'square');
}

function attack(){
  var pm=pickMob();
  var it=inv[cur];
  var td=toolInfo(it);

  if(pm&&pm.t<4){
    if(atkT>0)return true;

    var dmg=td&&td.tool==='sword'
      ?(td.mat===0?4:td.mat===1?5:6)
      :1;

    damageMob(pm.e,dmg,td&&td.tool==='sword'?6:3);

    if(td&&td.tool==='sword'){
      damageTool(cur,1);
    }

    atkT=.45;
    snd(180,.07,'sawtooth');
    swing();

    return true;
  }

  return false;
}

function act(button){
  if(dead||paused||panelShade.style.display==='flex')return;

  if(button===0){
    if(!attack())swing();
    return;
  }

  if(button===2){
    tryUse();
    swing();
  }
}

/* ---------- UI ---------- */

var panelShade=$('panelShade');
var invUI=$('invUI');
var furnaceUI=$('furnaceUI');
var chestUI=$('chestUI');
var craftGrid=$('craftGrid');
var craftOut=$('craftOut');

var hotEls=[],mainEls=[],invHotEls=[];

function mkUISlot(parent,arr,index,out,kind){
  var el=document.createElement('div');
  var c=document.createElement('canvas');
  var n=document.createElement('i');

  el.className='slot';
  c.width=c.height=16;

  el.appendChild(c);
  el.appendChild(n);
  parent.appendChild(el);

  el._o={
    el:el,
    c:c,
    n:n,
    arr:arr,
    index:index,
    out:out,
    kind:kind
  };

  el.addEventListener('mousedown',function(e){
    e.preventDefault();
    e.stopPropagation();
    slotMouse(el._o,e.button===2);
  });

  return el._o;
}

for(var h0=0;h0<9;h0++){
  var hs=mkUISlot($('bar'),inv,h0,false,'hot');
  var hb=document.createElement('b');
  hb.textContent=h0+1;
  hs.el.appendChild(hb);
  hotEls.push(hs);
}

for(var m0=9;m0<36;m0++){
  mainEls.push(mkUISlot($('invMain'),inv,m0,false,'main'));
}

for(var h1=0;h1<9;h1++){
  invHotEls.push(mkUISlot($('invHot'),inv,h1,false,'hot'));
}

var craftEls=[];

function renderCraftGrid(){
  craftGrid.innerHTML='';
  craftEls=[];

  var count=craft3?9:4;

  for(var i=0;i<9;i++){
    var s=mkUISlot(craftGrid,cg,i,false,'craft');
    s.el.style.display=i<count?'flex':'none';
    craftEls.push(s);
  }
}

renderCraftGrid();

var craftOutEl=mkUISlot(craftOut,null,0,true,'out');

var fIn=mkUISlot($('fIn'),null,0,false,'finput');
var fFuel=mkUISlot($('fFuel'),null,0,false,'ffuel');
var fOut=mkUISlot($('fOut'),null,0,false,'foutput');

var chestEls=[],chestInvEls=[],chestHotEls=[];

for(var c1=0;c1<27;c1++){
  chestEls.push(mkUISlot($('chestGrid'),null,c1,false,'chest'));
}

for(var c2=9;c2<36;c2++){
  chestInvEls.push(mkUISlot($('chestInv'),inv,c2,false,'main'));
}

for(var c3=0;c3<9;c3++){
  chestHotEls.push(mkUISlot($('chestHot'),inv,c3,false,'hot'));
}

function getSlotItem(o){
  if(o.kind==='finput')return furnace.input;
  if(o.kind==='ffuel')return furnace.fuel;
  if(o.kind==='foutput')return furnace.output;

  var arr=o.kind==='chest'
    ?(chests[chestOpenKey]||[])
    :inv;

  return arr[o.index]||null;
}

function putSlotItem(o,it){
  if(o.kind==='finput')furnace.input=it;
  else if(o.kind==='ffuel')furnace.fuel=it;
  else if(o.kind==='foutput')furnace.output=it;
  else{
    var arr=o.kind==='chest'?chests[chestOpenKey]:inv;
    arr[o.index]=it;
  }
}

function slotMouse(o,right){
  var it=getSlotItem(o);

  if(o.kind==='out'){
    var r=craftResult();

    if(r&&(!cursor||canStack(cursor,makeItem(r.out,r.n)))){
      if(!cursor)cursor=makeItem(r.out,r.n);
      else cursor.n+=r.n;

      consumeCraft(r);
    }

    refreshUI();
    return;
  }

  if(o.kind==='foutput'){
    if(!cursor&&furnace.output){
      cursor=furnace.output;
      furnace.output=null;
    }else if(
      cursor&&
      furnace.output&&
      canStack(cursor,furnace.output)&&
      cursor.n+furnace.output.n<=64
    ){
      cursor.n+=furnace.output.n;
      furnace.output=null;
    }

    refreshUI();
    return;
  }

  if(o.kind==='finput'&&cursor&&!furnaceRecipe(cursor.id)){
    say('That cannot be smelted');
    return;
  }

  if(o.kind==='ffuel'&&cursor&&!isFuel(cursor.id)){
    say('That is not furnace fuel');
    return;
  }

  if(!cursor){
    if(it){
      if(right&&it.n>1){
        var half=Math.ceil(it.n/2);
        var take=makeItem(it.id,half,it.d);
        it.n-=half;
        cursor=take;

        if(it.n<=0)putSlotItem(o,null);
      }else{
        cursor=it;
        putSlotItem(o,null);
      }
    }
  }else if(!it){
    if(right&&cursor.n>1){
      putSlotItem(o,makeItem(cursor.id,1,cursor.d));
      cursor.n--;
    }else{
      putSlotItem(o,cursor);
      cursor=null;
    }
  }else if(canStack(it,cursor)){
    var cap=64;
    var m=Math.min(cap-it.n,right?1:cursor.n);

    it.n+=m;
    cursor.n-=m;

    if(cursor.n<=0)cursor=null;
  }else{
    putSlotItem(o,cursor);
    cursor=it;
  }

  refreshUI();
}

function refreshSlot(o){
  var it=getSlotItem(o);
  var c=o.c.getContext('2d');

  c.clearRect(0,0,16,16);

  if(it){
    pixelIcon(c,itemIconTile(it.id));
    o.n.textContent=it.n>1?it.n:(ITEMS[it.id]?.max?Math.max(0,it.d||0):'');
  }else{
    o.n.textContent='';
  }
}

function renderCraftOutput(){
  var r=craftResult();
  var c=craftOutEl.c.getContext('2d');

  c.clearRect(0,0,16,16);
  craftOutEl.n.textContent='';

  if(r){
    pixelIcon(c,itemIconTile(r.out));
    craftOutEl.n.textContent=r.n>1?r.n:'';
  }
}

function renderFurnace(){
  $('smeltProgress').style.width=
    Math.min(
      100,
      furnace.total
        ?furnace.progress/furnace.total*100
        :0
    )+'%';
}

function renderChest(){}

function updateHand(){
  var c=$('hand').getContext('2d');

  c.clearRect(0,0,16,16);

  var it=inv[cur];

  if(it){
    pixelIcon(c,itemIconTile(it.id));
  }
}

function refreshUI(){
  hotEls
    .concat(mainEls,invHotEls,craftEls,[craftOutEl,fIn,fFuel,fOut],chestEls,chestInvEls,chestHotEls)
    .forEach(refreshSlot);

  for(var i=0;i<9;i++){
    hotEls[i].el.className='slot'+(i===cur?' sel':'');
  }

  $('bname').textContent=inv[cur]?itemName(inv[cur].id):'';

  updateHand();
  drawStats();
  renderCraftOutput();
  renderFurnace();
  renderChest();
}

function pixelIcon(c,tileNo){
  var x=tileNo%16;
  var y=(tileNo/16)|0;

  c.imageSmoothingEnabled=false;
  c.drawImage(
    atlas,
    x*16,y*16,16,16,
    0,0,16,16
  );
}

function select(i){
  cur=(i+9)%9;
  refreshUI();
}

/* ---------- saving / loading ---------- */

function serItem(o){
  return o?{id:o.id,n:o.n,d:o.d}:null;
}

function deItem(o){
  return o?makeItem(o.id,o.n,o.d):null;
}

function encodeMods(m){
  var s='';

  m.forEach(function(id,idx){
    s+=String.fromCharCode(
      (idx>>>8)&255,
      idx&255,
      id&255
    );
  });

  return btoa(s);
}

function decodeMods(s){
  var m=new Map();

  if(!s)return m;

  var raw=atob(s);

  for(var i=0;i+2<raw.length;i+=3){
    var idx=(raw.charCodeAt(i)<<8)|raw.charCodeAt(i+1);
    var id=raw.charCodeAt(i+2);
    m.set(idx,id);
  }

  return m;
}

function saveGame(){
  try{
    var mods=[];

    savedMods.forEach(function(m,k){
      if(m&&m.size){
        mods.push([k,encodeMods(m)]);
      }
    });

    var data={
      version:VERSION,
      seed:SEED,
      p:{
        x:P.x,
        y:P.y,
        z:P.z,
        yaw:P.yaw,
        pitch:P.pitch
      },
      spawn:spawnPoint,
      inv:inv.map(serItem),
      cg:cg.map(serItem),
      furnace:{
        input:serItem(furnace.input),
        fuel:serItem(furnace.fuel),
        output:serItem(furnace.output),
        burn:furnace.burn,
        progress:furnace.progress,
        total:furnace.total
      },
      chests:chests,
      hp:hp,
      food:food,
      sat:sat,
      exh:exh,
      armor:armor,
      xp:xp,
      level:level,
      life:life,
      tod:tod,
      mods:mods
    };

    localStorage.setItem(SAVEKEY,JSON.stringify(data));
    say('World saved');

    return true;
  }catch(e){
    say('Save failed: '+e.message);
    return false;
  }
}

function loadGame(){
  try{
    var raw=localStorage.getItem(SAVEKEY);

    if(!raw)return false;

    var d=JSON.parse(raw);

    if(!d||d.version!==VERSION)return false;

    SEED=d.seed;

    chunkCache.forEach(function(ch){
      gl.deleteBuffer(ch.ob);
      gl.deleteBuffer(ch.tb);
    });

    chunkCache.clear();
    genQueue=[];
    queued.clear();
    savedMods=new Map();

    (d.mods||[]).forEach(function(p){
      savedMods.set(p[0],decodeMods(p[1]));
    });

    Object.assign(P,d.p);

    spawnPoint=d.spawn
      ?{x:d.spawn.x,y:d.spawn.y,z:d.spawn.z}
      :{x:P.x,y:P.y,z:P.z};

    inv=(d.inv||[]).map(deItem);

    while(inv.length<36)inv.push(null);

    cg=(d.cg||[]).map(deItem);

    while(cg.length<9)cg.push(null);

    furnace={
      input:deItem(d.furnace?.input),
      fuel:deItem(d.furnace?.fuel),
      output:deItem(d.furnace?.output),
      burn:d.furnace?.burn||0,
      progress:d.furnace?.progress||0,
      total:d.furnace?.total||8
    };

    chests=d.chests||{};
    hp=d.hp==null?20:d.hp;
    food=d.food==null?20:d.food;
    sat=d.sat==null?5:d.sat;
    exh=d.exh||0;
    armor=d.armor||0;
    xp=d.xp||0;
    level=d.level||0;
    life=d.life||0;
    tod=d.tod==null?.28:d.tod;

    streamWorld(true);
    refreshUI();
    say('World loaded');

    return true;
  }catch(e){
    console.error(e);
    return false;
  }
}

window.addEventListener('beforeunload',function(){
  if(playing&&!dead)saveGame();
});

/* ---------- input / menus ---------- */

function returnFocus(){
  try{
    var r=cv.requestPointerLock();

    if(r&&r.catch){
      r.catch(function(){});
    }
  }catch(e){}
}

function startWorld(newWorld){
  if(newWorld){
    try{
      localStorage.removeItem(SAVEKEY);
    }catch(e){}

    SEED=(Math.random()*2147483647)|0;

    chunkCache.forEach(function(ch){
      gl.deleteBuffer(ch.ob);
      gl.deleteBuffer(ch.tb);
    });

    chunkCache.clear();
    genQueue=[];
    queued.clear();
    savedMods=new Map();

    P.x=0;
    P.z=0;

    resetStats();
    ensureChunk(0,0);
    spawn();

    inv.fill(null);

    addItem(4,1);
    addItem(119,2);
    addItem(2,8);
  }else if(!loadGame()){
    SEED=(Math.random()*2147483647)|0;

    chunkCache.forEach(function(ch){
      gl.deleteBuffer(ch.ob);
      gl.deleteBuffer(ch.tb);
    });

    chunkCache.clear();
    genQueue=[];
    queued.clear();
    savedMods=new Map();

    P.x=0;
    P.z=0;

    resetStats();
    ensureChunk(0,0);
    spawn();

    addItem(4,1);
    addItem(119,2);
    addItem(2,8);
  }

  playing=true;
  paused=false;
  dead=false;

  menu.style.display='none';
  hud.style.display=hideHUD?'none':'block';
  $('pause').style.display='none';

  streamWorld(true);
  returnFocus();
  refreshUI();
}

playBtn.addEventListener('click',function(){
  startWorld(false);
});

$('newGame').addEventListener('click',function(){
  startWorld(true);
});

$('resumeBtn').addEventListener('click',function(){
  paused=false;
  playing=true;
  $('pause').style.display='none';
  returnFocus();
});

$('saveBtn').addEventListener('click',saveGame);

$('menuBtn').addEventListener('click',function(){
  if(playing&&!dead)saveGame();

  playing=false;
  paused=false;

  $('pause').style.display='none';
  menu.style.display='flex';
  hud.style.display='none';

  if(document.exitPointerLock)document.exitPointerLock();
});

$('respawn').addEventListener('click',respawn);

$('deathMenu').addEventListener('click',function(){
  dead=false;
  playing=false;
  $('death').style.display='none';
  menu.style.display='flex';
  hud.style.display='none';
});

document.addEventListener('pointerlockchange',function(){
  locked=document.pointerLockElement===cv;

  if(locked){
    fallback=false;
  }else if(
    playing&&
    panelShade.style.display!=='flex'&&
    !paused&&
    !dead&&
    !fallback
  ){
    togglePause();
  }
});

document.addEventListener('pointerlockerror',function(){
  fallback=true;
  playing=true;
  menu.style.display='none';
  hud.style.display='block';
});

function togglePause(){
  if(dead)return;

  if(panelShade.style.display==='flex'){
    closePanel();
    return;
  }

  paused=!paused;
  playing=!paused;

  $('pause').style.display=paused?'flex':'none';

  if(paused&&document.exitPointerLock){
    document.exitPointerLock();
  }

  if(!paused)returnFocus();
}

document.addEventListener('keydown',function(e){
  if(['Space','ArrowUp','ArrowDown','ArrowLeft','ArrowRight'].indexOf(e.code)>=0){
    e.preventDefault();
  }

  keys[e.code]=true;

  if(panelShade.style.display==='flex'){
    if(e.code==='Escape'){
      closePanel();
      e.preventDefault();
    }

    return;
  }

  if(e.code==='KeyE'&&!e.repeat&&!dead){
    openPanel('inv');
  }else if(e.code==='Escape'&&!e.repeat){
    togglePause();
  }else if(e.code==='F5'){
    e.preventDefault();
    saveGame();
  }else if(e.code==='F9'){
    e.preventDefault();

    if(loadGame()){
      playing=true;
      paused=false;
      menu.style.display='none';
      hud.style.display='block';
      returnFocus();
    }
  }else if(e.code==='F11'){
    e.preventDefault();

    if(document.fullscreenElement){
      document.exitFullscreen();
    }else{
      document.documentElement.requestFullscreen?.();
    }
  }else if(e.code==='F1'){
    e.preventDefault();

    hideHUD=!hideHUD;
    $('hud').style.display=hideHUD?'none':(playing?'block':'none');
  }else if(e.code==='F3'){
    e.preventDefault();
    debug=!debug;
  }else if(e.code==='KeyQ'&&!e.repeat&&!dead){
    dropItemFromHotbar();
  }else{
    var d=parseInt(e.key,10);

    if(d>=1&&d<=9){
      select(d-1);
    }
  }
});

document.addEventListener('keyup',function(e){
  keys[e.code]=false;
});

window.addEventListener('blur',function(){
  keys={};
  held=-1;
});

document.addEventListener('mousemove',function(e){
  if(cursor&&panelShade.style.display==='flex'){
    var el=$('cur');
    el.style.left=(e.clientX-16)+'px';
    el.style.top=(e.clientY-16)+'px';
  }

  if(!locked||!playing||paused||dead)return;

  P.yaw-=(e.movementX||0)*.0024;
  P.pitch=Math.max(
    -1.52,
    Math.min(
      1.52,
      P.pitch-(e.movementY||0)*.0024
    )
  );
});

document.addEventListener('mousedown',function(e){
  if(
    !playing||
    paused||
    dead||
    panelShade.style.display==='flex'
  )return;

  e.preventDefault();

  if(!locked){
    returnFocus();
    return;
  }

  if(e.button===0||e.button===2){
    act(e.button);
    held=e.button;
    heldT=.23;
  }
});

document.addEventListener('mouseup',function(){
  held=-1;
});

document.addEventListener('contextmenu',function(e){
  e.preventDefault();
});

document.addEventListener('wheel',function(e){
  if(
    playing&&
    !paused&&
    !dead&&
    panelShade.style.display!=='flex'
  ){
    e.preventDefault();
    select(cur+(e.deltaY>0?1:-1));
  }
},{passive:false});

/* ---------- movement/update ---------- */

function inWater(){
  return get(
    Math.floor(P.x),
    Math.floor(P.y+.8),
    Math.floor(P.z)
  )===25;
}

function movePlayer(dt){
  K=(panelShade.style.display==='flex'||paused||dead)?{}:keys;

  var lk=(K.ArrowLeft?1:0)-(K.ArrowRight?1:0);
  var lu=(K.ArrowUp?1:0)-(K.ArrowDown?1:0);

  P.yaw+=lk*2.2*dt;
  P.pitch=Math.max(
    -1.52,
    Math.min(
      1.52,
      P.pitch+lu*1.8*dt
    )
  );

  var fw=(K.KeyW?1:0)-(K.KeyS?1:0);
  var rt=(K.KeyD?1:0)-(K.KeyA?1:0);

  var fx=-Math.sin(P.yaw);
  var fz=-Math.cos(P.yaw);
  var rx=Math.cos(P.yaw);
  var rz=-Math.sin(P.yaw);

  var wx=fx*fw+rx*rt;
  var wz=fz*fw+rz*rt;

  var len=Math.hypot(wx,wz);
  var swimming=inWater();

  sprinting=!!(
    K.ShiftLeft||
    K.ShiftRight
  )&&fw>0&&food>6&&!swimming;

  var sp=swimming?2.7:(sprinting?6.2:4.3);

  var tx=0,tz=0;

  if(len){
    tx=wx/len*sp;
    tz=wz/len*sp;
  }

  var ox=P.x,oz=P.z;
  var n=Math.ceil(dt/(1/90));

  for(var i=0;i<n;i++){
    var a=Math.min(
      1,
      (P.ground?14:3.5)*(dt/n)
    );

    P.vx+=(tx-P.vx)*a;
    P.vz+=(tz-P.vz)*a;

    P.vy=swimming
      ?Math.max(P.vy-7*(dt/n),-4)
      :Math.max(P.vy-28*(dt/n),-45);

    if(
      (K.Space&&P.ground)||
      (K.Space&&swimming)
    ){
      P.vy=swimming?5.2:8.6;
      P.ground=false;
      exh+=.2;
    }

    moveBody(P,dt/n,HW,PH);
  }

  if(
    Math.floor(P.x/CS)!==centerCX||
    Math.floor(P.z/CS)!==centerCZ
  ){
    streamWorld(false);
  }

  return Math.hypot(P.x-ox,P.z-oz);
}

function update(dt){
  if(!playing||paused||dead)return;

  saveClock+=dt;
  uiClock+=dt;

  var dist=movePlayer(dt);

  tod=(tod+dt/420)%1;

  updateSurvival(dt,dist);
  furnaceTick(dt);
  updateParticles(dt);
  updateItems(dt);
  updateProjectiles(dt);
  updateMobs(dt);

  T=raycast(
    P.x,
    P.y+EYE,
    P.z,
    dirVec()[0],
    dirVec()[1],
    dirVec()[2],
    6
  );

  atkT=Math.max(0,atkT-dt);

  if(held===0){
    if(!attack())mine(dt);
  }else if(held===2){
    heldT-=dt;

    if(heldT<=0){
      tryUse();
      heldT=.23;
    }
  }

  if(sayT>0){
    sayT-=dt;

    if(sayT<=0){
      $('toast').style.opacity=0;
    }
  }

  if(saveClock>=30){
    saveClock=0;
    saveGame();
  }

  iv=Math.max(0,iv-dt);
  fl=Math.max(0,fl-dt);

  $('flash').style.opacity=fl*1.5;

  var mw=$('mineW');

  mw.style.opacity=mineP>0?1:0;
  $('mine').style.width=Math.min(100,mineP*100)+'%';

  if(uiClock>=.05){
    uiClock=0;
    refreshUI();
  }
}

function updateSky(){
  var daylight=Math.max(
    0,
    Math.min(
      1,
      Math.sin(tod*6.283+.7)*.5+.5
    )
  );

  L=.27+.73*daylight;

  var fog=[
    .45+.10*daylight,
    .66+.15*daylight,
    .84+.12*daylight
  ];

  gl.clearColor(
    fog[0],
    fog[1],
    fog[2],
    1
  );

  gl.uniform3f(
    uFog,
    fog[0],
    fog[1],
    fog[2]
  );
}

function drawStats(){
  var key=hp+','+food+','+armor+','+xp+','+level;

  if(key===lastStats&&!debug)return;

  lastStats=key;

  var s=sc;

  s.clearRect(0,0,480,46);

  for(var i=0;i<10;i++){
    s.fillStyle='#222';
    s.fillRect(i*20,7,16,10);

    var av=armor-i*2;

    s.fillStyle='#c9cdd4';

    if(av>=2)s.fillRect(i*20,7,16,10);
    else if(av===1)s.fillRect(i*20,7,8,10);

    s.fillStyle='#551010';
    s.fillRect(i*20,25,16,12);

    var hv=hp-i*2;

    s.fillStyle='#df3030';

    if(hv>=2)s.fillRect(i*20,25,16,12);
    else if(hv===1)s.fillRect(i*20,25,8,12);

    s.fillStyle='#593214';
    s.fillRect(274+i*20,25,16,12);

    var fv=food-i*2;

    s.fillStyle='#e07b2e';

    if(fv>=2)s.fillRect(274+i*20,25,16,12);
    else if(fv===1)s.fillRect(274+i*20,25,8,12);
  }

  s.fillStyle='#222';
  s.fillRect(160,7,104,8);

  s.fillStyle='#58cf59';
  s.fillRect(
    160,
    7,
    104*Math.min(1,xp/xpNeed()),
    8
  );

  s.fillStyle='#fff';
  s.font='10px monospace';
  s.fillText('Lv '+level,205,15);

  if(debug){
    s.fillText(
      'XP '+xp+'/'+xpNeed(),
      160,
      44
    );
  }
}

function render(){
  resizeCanvas();

  gl.clear(
    gl.COLOR_BUFFER_BIT|
    gl.DEPTH_BUFFER_BIT
  );

  updateSky();

  var d=dirVec();

  var ex=P.x;
  var ez=P.z;

  var bob=(
    P.ground&&
    Math.hypot(P.vx,P.vz)>1
  )?Math.sin(life*12)*.035:0;

  var ey=P.y+EYE+bob;

  var fov=1.18+(sprinting?.08:0);

  var m=mul(
    persp(
      fov,
      cv.width/cv.height,
      .05,
      180
    ),
    look(
      ex,
      ey,
      ez,
      d[0],
      d[1],
      d[2]
    )
  );

  gl.uniformMatrix4fv(uM,false,m);
  gl.uniform1f(uTime,life);

  drawSkyObjects(ex,ey,ez);

  for(var i=0;i<activeChunks.length;i++){
    var ch=activeChunks[i];

    if(!ch.dirty&&ch.on){
      drawBuf(
        ch.ob,
        ch.on,
        gl.TRIANGLES
      );
    }
  }

  gl.enable(gl.BLEND);
  gl.blendFunc(
    gl.SRC_ALPHA,
    gl.ONE_MINUS_SRC_ALPHA
  );

  gl.depthMask(false);

  for(var j=0;j<activeChunks.length;j++){
    var ct=activeChunks[j];

    if(!ct.dirty&&ct.tn){
      drawBuf(
        ct.tb,
        ct.tn,
        gl.TRIANGLES
      );
    }
  }

  gl.depthMask(true);
  gl.disable(gl.BLEND);

  drawEntities();
  drawParts();

  if(T&&playing&&!paused&&!dead){
    drawOutline(T);
  }

  if(uiClock>=.05){
    uiClock=0;
    refreshUI();
  }
}

/* ---------- boot ---------- */

var last=performance.now();
var fps=0;
var fa=0;
var fc=0;
var T=null;
var saveClock=0;
var uiClock=0;
var lastStats='';

function init(){
  playBtn.textContent=(function(){
    try{
      return localStorage.getItem(SAVEKEY)
        ?'Continue / Load'
        :'Play World';
    }catch(e){
      return'Play World';
    }
  })();

  ensureChunk(0,0);
  spawn();

  addItem(4,1);
  addItem(119,2);
  addItem(2,8);

  refreshUI();

  setTimeout(function(){
    for(
      var n=0;
      n<80&&ents.length<8;
      n++
    ){
      var a=Math.random()*6.283;
      var r=15+Math.random()*32;

      spawnMob(
        ['cow','pig','sheep','chicken'][(Math.random()*4)|0],
        Math.floor(P.x+Math.cos(a)*r),
        Math.floor(P.z+Math.sin(a)*r)
      );
    }

    streamWorld(true);
    requestAnimationFrame(frame);
  },30);
}

function frame(now){
  requestAnimationFrame(frame);

  try{
    var dt=Math.min(.1,(now-last)/1000);

    last=now;

    if(playing&&!paused&&!dead){
      life+=dt;
    }

    worldFrame++;

    update(dt);
    streamWorld(false);
    processChunkQueue(2);

    var built=0;

    for(
      var i=0;
      i<activeChunks.length&&built<3;
      i++
    ){
      if(activeChunks[i].dirty){
        build(activeChunks[i]);
        built++;
      }
    }

    render();

    fa+=dt;
    fc++;

    if(fa>.5){
      fps=Math.round(fc/fa);
      fa=0;
      fc=0;

      var b=biomeAt(
        Math.floor(P.x),
        Math.floor(P.z)
      );

      $('info').textContent=
        'FPS: '+fps+
        (debug
          ?'\nXYZ: '+P.x.toFixed(1)+
           ' / '+P.y.toFixed(1)+
           ' / '+P.z.toFixed(1)+
           '\nChunk: '+Math.floor(P.x/CS)+
           ','+Math.floor(P.z/CS)+
           '\nSeed: '+SEED+
           '\nBiome: '+['','Plains','Forest','Desert','Snow'][b]
          :'');
    }
  }catch(e){
    if(!errShown){
      errShown=true;
      playing=false;
      menu.style.display='flex';
      hud.style.display='none';
      fail('Runtime error: '+e.message);
      console.error(e);
    }
  }
}

$('hud').style.display='none';

init();

})();
</script>
</body>
</html>