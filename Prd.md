# Knight of the Emerald Keep

Open game.html in a browser to play.

- Left/right arrows move, Up jumps, Space swings the sword, P pauses.
- Each sword swing requires a fresh press or touch. Holding Space never repeats attacks. Normal cooldown is 0.7 seconds; presses during cooldown are ignored.
- Level 1 has 3 waves, level 2 has 4, level 3 has 5, and each later level adds one wave. Each wave has 10 slimes followed by a boss.
- Bosses rotate by wave and level: crowned green Moss King leaps and creates ground shockwaves; orange horned Ember Ram charges; purple Crystal Oracle fires an aimed three-bolt volley. Each attack has a visible warning and recovery window.
- Start with 5 hearts. Enemy contact or a boss attack costs one heart, followed by brief protection. Recover one heart per wave and all hearts per level.
- Slime and boss health increase with levels. Five themed regions repeat as harder ascensions.
- Journey and knight statistics track progression and combat. Best level saves in browser storage when available.
- Secret code mo grants 100 hearts, no cooldown, and half-max-health damage. Sword swings still require separate presses.
- Secret code op unlocks J projectiles and a touch Fire button. Holding J repeats shots. It combines with mo. Codes remain active through restarts until the page reloads.
- Restart and wave changes clear projectiles and boss hazards. Pause freezes combat.



- Press either Shift key or the Shield touch button to spend one charge for 5 seconds of protection. Releasing the button does not end the shield. Two charges are available per level; only a new level or restart refills them. Holding Shift cannot spend a second charge. The HUD shows remaining charges and active time. Shielding slows movement and prevents attacks; pausing freezes the timer.

- The hidden hint is small, faint graffiti on an ordinary background stone, without a sign or label.
- Regular enemies include basic slimes, fast Runners, high-jumping Hoppers, Armored slimes with extra health, and ranged Spitters. The first three appear in level 1; Armored slimes enter in level 2 and Spitters in level 3. Each wave still totals 10 enemies.
- Two additional bosses join the rotation: Frost Warden drops ice over marked locations, and Thorn Matriarch grows delayed thorn patches. Both have unique silhouettes, warning markers, and recovery periods.

## Royal theme

- Use purple-and-gold panels, controls, and HUD elements, elegant serif headings, gilded castle scenery, and crown banners. Preserve the existing combat and controls.

## Score, leaderboard, and timer

- Display the current score, high score, and active-run timer in the HUD from the initial screen onward.
- Award 100 points per regular enemy defeated, 500 per boss defeated, 250 per wave cleared, and 1,000 per completed level. Sword and projectile defeats both count. Score is calculated from run statistics and completed levels, so each reward counts once.
- Start every run at score 0 and time 00:00. Display elapsed time as minutes:seconds, allowing minutes above 59. Count actual elapsed active play time, including wave recovery; pause, lost window focus, and game over freeze the timer.
- Let players enter a knight name of up to 24 characters before starting. Blank names become Anonymous Knight. Capture the name at the start of each run. Focusing the name input pauses active play.
- Save each run once when the knight falls or when Restart ends an active/paused run. Restarting after game over must not create a duplicate entry. Do not record an unstarted run.
- Show a Royal leaderboard table containing rank, knight name, score, level reached, elapsed time, and Standard/Assisted mode.
- Store the top 10 runs locally under knight-leaderboard-v1. Sort by descending score, then descending level, then shorter elapsed time. The high score shows the greater of the current score and the best saved score.
- Mark runs using mo or op as Assisted. Include both modes in the table with an explicit label.
- Validate stored entries before rendering. Render names as text, never HTML. If storage is unavailable or fails, keep scores in memory and explain that they last for this session.
- The leaderboard is local to this browser, not an online leaderboard. Saved runs persist across reloads when browser storage is available. Keep existing best-level storage intact.
- Match the royal theme, use semantic table headers and a caption, and allow horizontal scrolling within the table on small screens.

### Verification

- Confirm a slime defeat gives 100 points; a boss defeat gives 500; clearing a full wave totals 1,750; completing level 1's three waves totals 6,250.
- Confirm the timer advances during play, freezes when paused or after death, and resets on restart.
- Finish or restart multiple runs and verify descending rankings, tie ordering, and the top-10 limit.
- Reload and verify saved scores remain. Check empty, malformed, and unavailable storage behavior.
- Confirm a defeated run is not saved twice when Restart is pressed. Verify names containing HTML characters display as plain text.
- Activate either secret code and verify the saved run is labeled Assisted.

## Complete game code

```html
<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>Knight of the Emerald Keep</title>
<style>
*{box-sizing:border-box}body{margin:0;background:#101b24;color:#e8eee3;font-family:system-ui,sans-serif;min-height:100vh;display:grid;place-items:center}main{width:min(100% - 24px,1000px);padding:24px 0}header{display:flex;justify-content:space-between;align-items:center;gap:20px}h1{font-family:Georgia,serif;font-size:clamp(24px,4vw,40px);margin:6px 0 18px}small{color:#afcc8b;letter-spacing:3px}button{background:#c6e398;color:#15271a;border:0;border-radius:8px;padding:12px 20px;font-weight:bold;cursor:pointer}button:focus-visible{outline:3px solid white;outline-offset:4px}.hud{display:flex;justify-content:space-between;gap:12px;flex-wrap:wrap;padding:14px;background:#203039;border:1px solid #465445;border-radius:10px 10px 0 0}#hearts{color:#ff9191}canvas{display:block;width:100%;background:#172d35;border:1px solid #465445;border-top:0;border-radius:0 0 10px 10px}p{color:#b2c2bc;line-height:1.6}kbd{color:#eff7df;background:#2a3b41;padding:3px 7px;border-radius:4px}.controls{display:flex;gap:10px;justify-content:center;touch-action:none}.controls button{min-width:60px;user-select:none}#status{min-height:26px;color:#d3eaa9;text-align:center}
.panels{display:grid;grid-template-columns:1fr 1fr;gap:16px;margin-top:20px}.panel{background:#203039;border:1px solid #465445;border-radius:10px;padding:18px}.panel h2{margin:0 0 12px;font-size:18px}.panel ol{padding-left:22px;line-height:1.9}.panel li.active{color:#d7f7a5;font-weight:bold}.panel li.done{color:#8cbca4}dl{display:grid;grid-template-columns:1fr auto;gap:8px;margin:0}dd{margin:0;color:#d7f7a5}progress{width:100%;accent-color:#afd576}@media(max-width:640px){.panels{grid-template-columns:1fr}} input{background:#15272e;color:#fff;border:1px solid #829a78;border-radius:6px;padding:11px;font:inherit;max-width:180px}.code-form{display:flex;align-items:center;gap:10px;flex-wrap:wrap;margin-top:20px} .menu-link{display:inline-block;margin-bottom:18px;padding:10px 16px;border:1px solid #829a78;border-radius:8px;color:#d7f7a5;text-decoration:none;font-weight:bold}.menu-link:hover{background:#2a3b41}.menu-link:focus-visible{outline:3px solid white;outline-offset:4px}</style>
<style>
:root{color-scheme:dark;--gold:#e8c778;--cream:#fff3d6;--muted:#c9bbd4}
body{background:radial-gradient(ellipse at top,#51315d,transparent 65%),#160c22;color:var(--cream);font-family:'Trebuchet MS',sans-serif}
main{padding-top:32px;padding-bottom:40px}h1,h2{font-family:Georgia,'Times New Roman',serif;color:var(--cream);font-weight:normal}h1{letter-spacing:.02em}header small,.sub{color:var(--gold)}p,small{color:var(--muted);line-height:1.6}a,.menu-link{color:var(--gold)}.menu-link,main>a:first-child{display:inline-block;padding:10px 16px;border:1px solid #b5904f;border-radius:6px;text-decoration:none;background:#2c193b}.menu-link:hover,main>a:first-child:hover{background:#40264f}
button{background:linear-gradient(135deg,#f1d696,#ceaa59);color:#2a1736;border:1px solid #f7dfa0;border-radius:6px;font-weight:bold}button:hover:enabled{background:linear-gradient(135deg,#ffe8b1,#e7c779)}button:focus-visible,a:focus-visible,input:focus-visible,select:focus-visible{outline:3px solid var(--cream);outline-offset:4px}input,select{background:#251431;color:var(--cream);border:1px solid #b5904f}
.panel,.hud{background:linear-gradient(145deg,#392246,#21132f);border:1px solid #b5904f;box-shadow:inset 0 0 0 4px #e8c77805,0 10px 30px #0002}.panel h2::before{content:'♛ ';color:var(--gold)}canvas{border-color:#b5904f;background:#21132f}.hud{padding:16px;border-radius:10px}.hud small{color:var(--muted)}#hearts{color:#ffa9ba}#status,.notice,#battle-status,dd,.panel li.active{color:var(--gold)}.panel li.done{color:#a4d7bc}kbd{background:#392246;color:var(--cream);border:1px solid #b5904f55}progress{accent-color:var(--gold)}#hud2 progress{accent-color:#c29ae8}.card{background:linear-gradient(145deg,#432952,#281735);color:var(--cream);border-color:#8c6a47}.card:hover:enabled{background:#51315d}.card.p1{outline-color:var(--gold)}.card.p2{box-shadow:inset 0 0 0 3px #c29ae8}.card:disabled{opacity:.65}.map{background:linear-gradient(145deg,#432952,#281735);color:var(--cream)}.map:hover:enabled{background:#51315d}#result{background:#1b0c2af0;border:1px solid #b5904f}#result h2{color:var(--gold)}
@media(max-width:640px){header{flex-wrap:wrap;gap:8px}.hud{gap:12px}main{padding-top:22px}}
.scoreboard{margin-top:20px}.table-scroll{overflow-x:auto}table{width:100%;border-collapse:collapse;text-align:left;font-variant-numeric:tabular-nums}caption{text-align:left;color:var(--muted);padding-bottom:12px}th,td{padding:10px;border-bottom:1px solid #e8c77830}th{color:var(--gold)}#score,#high-score,#timer{font-variant-numeric:tabular-nums;color:var(--gold)}.scoreboard input{max-width:220px}#leaderboard-note{min-height:24px}
</style></head>
<body>
<main>
<nav aria-label="Game navigation"><a class="menu-link" href="index.html">← Back to menu</a></nav>
<header><div><small>♛ THE ROYAL COLLECTION / SURVIVAL</small><h1>Knight of the Emerald Keep</h1></div><button id="start">Begin royal quest</button></header>
<div class="hud"><span id="hearts" aria-label="5 hearts">♥ ♥ ♥ ♥ ♥</span><span id="level">Level 1 · Wave 1 / 3</span><span id="shield-status">Shield: 2 / 2 charges</span><span id="count">10 slimes + 1 boss per wave</span><span id="score">Score: 0</span><span id="high-score">High score: 0</span><span id="timer">Time: 00:00</span></div>
<canvas id="game" width="960" height="480" aria-label="Knight survival game. Left and right arrows move, up jumps, space attacks, press Shift for a five-second shield." role="img"></canvas>
<p id="status" role="status">Defend the keep. Defeat 10 slimes, then their boss, in every wave.</p>
<div class="controls" aria-label="Touch controls"><button data-key="ArrowLeft" aria-label="Move left">←</button><button data-key="ArrowRight" aria-label="Move right">→</button><button data-key="ArrowUp" aria-label="Jump">↑ Jump</button><button data-key="Space">⚔ Attack</button><button data-key="ShiftLeft" aria-label="Activate shield for five seconds">⇧ Shield</button><button data-key="KeyJ" id="fire" hidden>J Fire</button></div>
<p><kbd>←</kbd> <kbd>→</kbd> Move &nbsp; <kbd>↑</kbd> Jump &nbsp; <kbd>Space</kbd> Sword &nbsp; <kbd>Shift</kbd> Shield &nbsp; <kbd>P</kbd> Pause &nbsp; <kbd>J</kbd> Fire (when unlocked)<br>Each hit costs one heart. Level 1 has 3 waves; each new level adds one wave. Tap Space for each sword swing (0.7-second cooldown). Press Shift for a 5-second shield. You have 2 charges per level; you move slower and cannot attack while shielding. Recover one heart after a wave and all hearts after a level.</p>
<section class="panels" aria-label="Progression and statistics">
<div class="panel"><h2>Kingdom journey</h2><ol id="journey"></ol><label id="progress-label" for="progress">Waves cleared: 0 / 3</label><progress id="progress" max="3" value="0"></progress><p id="enemy-stats"></p></div>
<div class="panel"><h2>Knight stats</h2><dl id="stats"></dl><p id="save-note">Best level is saved in this browser.</p></div>
</section>
<section class="panel scoreboard" aria-labelledby="leaderboard-heading">
<h2 id="leaderboard-heading">Royal leaderboard</h2>
<label for="knight-name">Knight name</label> <input id="knight-name" maxlength="24" placeholder="Anonymous Knight" autocomplete="off">
<p>100 points per slime · 500 per boss · 250 per wave · 1,000 per completed level.</p>
<div class="table-scroll"><table><caption>Top 10 runs on this browser. Runs save when you fall or restart; secret-code runs are marked Assisted.</caption><thead><tr><th scope="col">Rank</th><th scope="col">Knight</th><th scope="col">Score</th><th scope="col">Level</th><th scope="col">Time</th><th scope="col">Mode</th></tr></thead><tbody id="leaderboard"></tbody></table></div>
<p id="leaderboard-note" role="status"></p>
</section>
<form id="code-form" class="code-form"><label for="code">Ancient inscription</label><input id="code" type="text" autocomplete="off" spellcheck="false" placeholder="Try an inscription"><button type="submit">Activate</button><span id="code-message" role="status"></span></form>
</main>
<script>
'use strict';
const canvas=document.querySelector('#game'),ctx=canvas.getContext('2d');
const status=document.querySelector('#status'),start=document.querySelector('#start');
const keys=new Set(),floor=396;let moMode=false,opMode=false;let projectiles=[],shotCooldown=0,hazards=[],shieldCharges=2,shieldTime=0;const wavesForLevel=()=>level+2;
const bossTypes=[{name:"Moss King",color:"#58a563",attack:"Jump over the landing shockwaves!"},{name:"Ember Ram",color:"#e08a50",attack:"Jump over its charge!"},{name:"Crystal Oracle",color:"#a17cdf",attack:"Dodge its magic bolts!"},{name:"Frost Warden",color:"#75bed2",attack:"Move away from the falling ice markers!"},{name:"Thorn Matriarch",color:"#cc7798",attack:"Jump over the marked thorn patches!"}];const maxHearts=()=>moMode?100:5;
const mobTypes=[
 {name:'Slime',color:'#83ca5c',size:32,health:0,speed:1,jump:230},
 {name:'Runner',color:'#b5d956',size:25,health:0,speed:1.65,jump:130},
 {name:'Hopper',color:'#5fcba8',size:30,health:0,speed:.85,jump:430},
 {name:'Armored',color:'#75988a',size:42,health:1,speed:.65,jump:170},
 {name:'Spitter',color:'#d0ba62',size:34,health:0,speed:.7,jump:180}
];
const regions=[{name:'Emerald Keep',sky:'#496453'},{name:'Mosswood Forest',sky:'#254d39'},{name:'Moonlit Marsh',sky:'#39516f'},{name:'Ashen Fortress',sky:'#73473c'},{name:'Royal Citadel',sky:'#65517e'}];
let stats={slimes:0,bosses:0,damage:0,hits:0,waves:0,seconds:0},best=1,storageAvailable=true;
try{const saved=Number(localStorage.getItem('knight-best-level'));if(Number.isSafeInteger(saved)&&saved>0)best=saved;}catch{storageAvailable=false;}
const leaderboardKey='knight-leaderboard-v1';
let leaderboard=[],runRecorded=false,runName='Anonymous Knight';
function validRun(r){return r&&typeof r.name==='string'&&r.name.length<=24&&Number.isSafeInteger(r.score)&&r.score>=0&&Number.isSafeInteger(r.level)&&r.level>=1&&Number.isFinite(r.seconds)&&r.seconds>=0&&typeof r.assisted==='boolean';}
function rankRuns(runs){return runs.sort((a,b)=>b.score-a.score||b.level-a.level||a.seconds-b.seconds).slice(0,10);}
try{const saved=JSON.parse(localStorage.getItem(leaderboardKey)||'[]');if(Array.isArray(saved))leaderboard=rankRuns(saved.filter(validRun));}catch{storageAvailable=false;}
function score(){return stats.slimes*100+stats.bosses*500+stats.waves*250+(level-1)*1000;}
function formatTime(seconds){const total=Math.floor(seconds);return `${String(Math.floor(total/60)).padStart(2,'0')}:${String(total%60).padStart(2,'0')}`;}
function renderScore(){
 document.querySelector('#score').textContent=`Score: ${score().toLocaleString()}`;
 document.querySelector('#high-score').textContent=`High score: ${Math.max(score(),leaderboard[0]?.score||0).toLocaleString()}`;
 document.querySelector('#timer').textContent=`Time: ${formatTime(stats.seconds)}`;
}
function renderLeaderboard(){
 const body=document.querySelector('#leaderboard');body.replaceChildren();
 if(!leaderboard.length){const row=document.createElement('tr'),cell=document.createElement('td');cell.colSpan=6;cell.textContent='No scores yet. Begin your royal quest!';row.append(cell);body.append(row);}
 leaderboard.forEach((r,i)=>{const row=document.createElement('tr');for(const value of [i+1,r.name,r.score.toLocaleString(),r.level,formatTime(r.seconds),r.assisted?'Assisted':'Standard']){const cell=document.createElement('td');cell.textContent=value;row.append(cell);}body.append(row);});
 document.querySelector('#leaderboard-note').textContent=storageAvailable?'Scores are saved locally in this browser.':'Browser saving is unavailable. Scores are kept for this session.';
}
function recordRun(){
 if(!player||runRecorded)return;
 runRecorded=true;
 if(stats.seconds===0&&score()===0)return;
 leaderboard=rankRuns([...leaderboard,{name:runName,score:score(),level,seconds:stats.seconds,assisted:moMode||opMode}]);
 try{localStorage.setItem(leaderboardKey,JSON.stringify(leaderboard));}catch{storageAvailable=false;}
 renderLeaderboard();renderScore();
}
document.querySelector('#knight-name').addEventListener('focus',()=>{keys.clear();if(state==='playing')togglePause();});
function region(){return regions[(level-1)%regions.length];}
function levelName(){return region().name+(level>5?` · Ascension ${Math.floor((level-1)/5)+1}`:'');}
function recordBest(){if(level<=best)return;best=level;try{localStorage.setItem('knight-best-level',String(best));}catch{storageAvailable=false;}}
function renderStats(){
 const tier=Math.floor((level-1)/5)*5;
 document.querySelector('#journey').innerHTML=regions.map((r,i)=>{const n=tier+i+1;return `<li class="${n===level?'active':n<level?'done':''}" ${n===level?'aria-current="step"':''}>Level ${n}: ${r.name}${n<level?' — cleared':n===level?' — current':''}</li>`;}).join('');
 const cleared=wave-1+(transition>0?1:0);
 document.querySelector('#progress').max=wavesForLevel();document.querySelector('#progress').value=cleared;
 document.querySelector('#progress-label').textContent=`Waves cleared: ${cleared} / ${wavesForLevel()}`;
 document.querySelector('#enemy-stats').textContent=`Slime health: ${level}–${level+1} · Boss health: ${5+level*3+Math.floor(wave/3)} · Enemy contact: 1 heart. After the Citadel, a harder ascension begins.`;
 const values=[['Hearts',`${player?player.hp:maxHearts()} / ${maxHearts()}`],['Sword damage',moMode?'Half enemy max health':'1 per hit'],['Attack cooldown',moMode?'None · tap each swing':'0.7 seconds'],['Slimes defeated',stats.slimes],['Bosses defeated',stats.bosses],['Waves cleared',stats.waves],['Sword hits',stats.hits],['Hearts lost',stats.damage],['Time survived',`${Math.floor(stats.seconds/60)}m ${Math.floor(stats.seconds%60)}s`],['Best level',best]];
 document.querySelector('#stats').innerHTML=values.map(([label,value])=>`<dt>${label}</dt><dd>${value}</dd>`).join('');
 document.querySelector('#save-note').textContent=storageAvailable?'Best level is saved in this browser. Restart resets current adventure stats.':'Browser saving is unavailable. Best level is kept for this session.';
}
let player,enemies=[],particles=[],level=1,wave=1,kills=0,spawned=0,spawnTimer=0,bossSpawned=false;
let state='ready',transition=0,time=0,last=0,swingId=0;
function reset(){recordRun();runRecorded=false;runName=document.querySelector('#knight-name').value.trim().slice(0,24)||'Anonymous Knight';shieldCharges=2;shieldTime=0;player={x:460,y:floor-52,w:30,h:52,vy:0,face:1,hp:maxHearts(),inv:0,attack:0,cool:0};level=1;wave=1;stats={slimes:0,bosses:0,damage:0,hits:0,waves:0,seconds:0};particles=[];swingId=0;keys.clear();newWave();state='playing';start.textContent='Restart';hud();}
function newWave(){hazards=[];projectiles=[];shotCooldown=0;enemies=[];kills=0;spawned=0;spawnTimer=.6;bossSpawned=false;transition=0;status.textContent=`${levelName()} — level ${level}, wave ${wave}: defeat 10 slimes and their boss.`;}
function hud(){renderScore();renderStats();document.querySelector("#shield-status").textContent="Shield: "+shieldCharges+" / 2 charges"+(shieldTime>0?" · "+shieldTime.toFixed(1)+"s active":"");document.querySelector('#hearts').textContent=moMode?("♥ " + player.hp + " / 100"):'♥ '.repeat(player.hp)+'♡ '.repeat(5-player.hp);document.querySelector('#hearts').setAttribute('aria-label',`${player.hp} hearts`);document.querySelector('#level').textContent=`Level ${level}: ${levelName()} · Wave ${wave} / ${wavesForLevel()}`;document.querySelector('#count').textContent=`Slimes ${kills} / 10 · Boss ${bossSpawned?(enemies.some(e=>e.boss)?'fighting':'defeated'):'waiting'}`;}
function spawn(boss=false){const mob=(spawned+wave+level-2)%Math.min(5,level+2),type=mobTypes[mob];const size=boss?76:type.size;const hp=boss?5+level*3+Math.floor(wave/3):level+type.health;enemies.push({x:Math.random()<.5?-size:960,y:floor-size,w:size,h:size,hp,max:hp,boss,mob,spit:2.5,kind:(level+wave-2)%bossTypes.length,phase:"walk",timer:2,chargeDir:1,vy:0,jump:1+Math.random()*2,speed:boss?48+level*9:(48+level*12+wave*2)*type.speed,hit:0,lastSwing:-1});}
function overlap(a,b){return a.x<b.x+b.w&&a.x+a.w>b.x&&a.y<b.y+b.h&&a.y+a.h>b.y;}
function burst(x,y,color){for(let i=0;i<10;i++)particles.push({x,y,vx:(Math.random()-.5)*210,vy:-Math.random()*180,life:.5,color});}
function togglePause(){if(state==='playing'){state='paused';keys.clear();status.textContent='Paused. Press P to continue.';}else if(state==='paused'){state='playing';status.textContent='Defend the keep!';}}
addEventListener('keydown',e=>{if(e.target.matches('input,textarea,select'))return;if(['ArrowLeft','ArrowRight','ArrowUp','ArrowDown','Space','KeyJ','ShiftLeft','ShiftRight'].includes(e.code)){if(e.target.tagName==='BUTTON')return;e.preventDefault();if(e.code==='Space'){if(!e.repeat&&!keys.has('Space'))trySword();}if((e.code==='ShiftLeft'||e.code==='ShiftRight')&&!e.repeat&&!keys.has('ShiftLeft')&&!keys.has('ShiftRight'))activateShield();keys.add(e.code);}if(e.code==='KeyP'&&!e.repeat)togglePause();});
addEventListener('keyup',e=>keys.delete(e.code));
addEventListener('blur',()=>{keys.clear();if(state==='playing')togglePause();});
start.onclick=()=>{reset();start.blur();};
document.querySelectorAll('[data-key]').forEach(b=>{b.addEventListener('pointerdown',e=>{e.preventDefault();b.setPointerCapture(e.pointerId);if(b.dataset.key==="Space"&&!keys.has("Space"))trySword();if(b.dataset.key==="ShiftLeft"&&!keys.has("ShiftLeft")&&!keys.has("ShiftRight"))activateShield();keys.add(b.dataset.key);});for(const event of ['pointerup','pointercancel','lostpointercapture'])b.addEventListener(event,()=>keys.delete(b.dataset.key));});
function shielding(){return state==='playing'&&shieldTime>0;}
function activateShield(){
 if(state!=='playing'||transition>0||shieldTime>0||shieldCharges<=0)return;
 shieldCharges--;shieldTime=5;player.attack=0;hud();
}
function trySword(){
 if(state!=='playing'||transition>0||player.cool>0||shielding())return;
 player.attack=.2;player.cool=moMode?0:.7;swingId++;
}
function hurtPlayer(sourceX){
 if(player.inv>0||state!=='playing')return;
 if(shielding()){player.inv=.15;burst(player.x+player.w/2,player.y+25,'#8bdcff');return;}
 player.hp--;stats.damage++;player.inv=1.2;
 player.x=Math.max(0,Math.min(930,player.x+(player.x<sourceX?-32:32)));
 burst(player.x,player.y+20,'#ff9393');
 if(player.hp===0){state='over';recordRun();status.textContent=`You fell at level ${level}, wave ${wave}. Press Restart to try again.`;}
}
function moveBoss(e,dt){
 e.timer-=dt;
 if(e.phase==='walk'){
  e.x+=Math.sign(player.x+15-e.x-e.w/2)*e.speed*.6*dt;
  if(e.timer<=0){e.phase='warn';e.timer=.8;e.chargeDir=Math.sign(player.x+15-e.x-e.w/2)||1;}
 }else if(e.phase==='warn'&&e.timer<=0){
  if(e.kind===0){e.phase='air';e.vy=-540;}
  if(e.kind===1){e.phase='charge';e.timer=.7;}
  if(e.kind===2){
   const dx=player.x+15-e.x-e.w/2,dy=player.y+30-e.y-e.h/2,len=Math.hypot(dx,dy)||1;
   for(const angle of [-.22,0,.22])hazards.push({x:e.x+e.w/2,y:e.y+e.h/2,w:14,h:14,vx:(dx*Math.cos(angle)-dy*Math.sin(angle))/len*235,vy:(dx*Math.sin(angle)+dy*Math.cos(angle))/len*235,life:5,color:'#d5a5ff'});
   e.phase='recover';e.timer=1.3;
  }
  if(e.kind===3){
   for(const offset of [-100,0,100])hazards.push({x:Math.max(10,Math.min(928,player.x+offset)),y:40,w:22,h:38,vx:0,vy:330,life:3,delay:.8,color:'#b4f1ff'});
   e.phase='recover';e.timer=1.7;
  }
  if(e.kind===4){
   for(const offset of [-125,0,125])hazards.push({x:Math.max(0,Math.min(912,player.x+offset)),y:floor-38,w:48,h:38,vx:0,vy:0,life:1.5,delay:.9,color:'#e19db0'});
   e.phase='recover';e.timer=2;
  }
 }else if(e.phase==='charge'){
  e.x+=e.chargeDir*440*dt;
  if(e.timer<=0||e.x<=0||e.x>=960-e.w){e.phase='recover';e.timer=1.2;}
 }else if(e.phase==='recover'&&e.timer<=0){e.phase='walk';e.timer=1.5;}
 e.x=Math.max(0,Math.min(960-e.w,e.x));
 e.vy+=1000*dt;e.y+=e.vy*dt;
 if(e.y>=floor-e.h){
  e.y=floor-e.h;e.vy=0;
  if(e.phase==='air'){
   for(const dir of [-1,1])hazards.push({x:e.x+e.w/2,y:floor-16,w:30,h:16,vx:dir*270,vy:0,life:4,color:'#b9ea87'});
   burst(e.x+e.w/2,floor,'#b9ea87');e.phase='recover';e.timer=1.4;
  }
 }
}
function update(dt,elapsed=dt){
 time+=dt;if(state!=='playing')return;stats.seconds+=elapsed;shieldTime=Math.max(0,shieldTime-dt);
 for(const p of particles){p.life-=dt;p.x+=p.vx*dt;p.y+=p.vy*dt;p.vy+=480*dt;}particles=particles.filter(p=>p.life>0);
 player.inv=Math.max(0,player.inv-dt);player.cool=Math.max(0,player.cool-dt);player.attack=Math.max(0,player.attack-dt);
 if(shielding())player.attack=0;
 const dir=Number(keys.has('ArrowRight'))-Number(keys.has('ArrowLeft'));if(dir){player.face=dir;player.x=Math.max(0,Math.min(930,player.x+dir*(shielding()?120:260)*dt));}
 if(keys.has('ArrowUp')&&player.y>=floor-player.h){player.vy=-510;keys.delete('ArrowUp');}
 player.vy+=1300*dt;player.y+=player.vy*dt;if(player.y>=floor-player.h){player.y=floor-player.h;player.vy=0;}

 if(transition>0){transition-=dt;if(transition<=0){wave++;if(wave>wavesForLevel()){level++;shieldCharges=2;shieldTime=0;recordBest();wave=1;player.hp=maxHearts();}else player.hp=Math.min(maxHearts(),player.hp+1);newWave();}hud();return;}
 shotCooldown=Math.max(0,shotCooldown-dt);
 if(opMode&&!shielding()&&keys.has('KeyJ')&&shotCooldown===0){
  projectiles.push({x:player.face>0?player.x+player.w:player.x-18,y:player.y+30,w:18,h:10,vx:player.face*620});
  shotCooldown=moMode?0:.22;
 }
 for(const shot of projectiles){
  shot.x+=shot.vx*dt;
  const target=enemies.filter(e=>e.hp>0&&overlap(shot,e)).sort((a,b)=>shot.vx>0?a.x-b.x:b.x-a.x)[0];
  if(target){
   target.hp-=moMode?target.max/2:1;target.hit=.24;shot.dead=true;
   burst(target.x+target.w/2,target.y+target.h/2,'#83e9ff');
   if(target.hp<=0){if(target.boss)stats.bosses++;else{kills++;stats.slimes++;}}
  }
 }
 projectiles=projectiles.filter(shot=>!shot.dead&&shot.x+shot.w>0&&shot.x<960);
 enemies=enemies.filter(e=>e.hp>0);
 spawnTimer-=dt;if(spawned<10&&spawnTimer<=0){spawn();spawned++;spawnTimer=Math.max(.45,1.25-level*.07);}
 if(kills===10&&!bossSpawned){spawn(true);bossSpawned=true;const type=bossTypes[enemies.find(e=>e.boss).kind];status.textContent=type.name+' approaches! '+type.attack;}
 for(const e of enemies){e.hit=Math.max(0,e.hit-dt);if(e.boss){moveBoss(e,dt);}else{e.x+=Math.sign(player.x+15-e.x-e.w/2)*e.speed*dt;e.jump-=dt;if(e.jump<=0&&e.y>=floor-e.h){e.vy=-mobTypes[e.mob].jump;e.jump=e.mob===2?1.15:2+Math.random();}e.vy+=1000*dt;e.y+=e.vy*dt;if(e.y>=floor-e.h){e.y=floor-e.h;e.vy=0;}}
 if(!e.boss&&e.mob===4){
  e.spit-=dt;
  if(e.spit<=0&&e.x>0&&e.x<960){
   const dx=player.x+15-e.x-e.w/2,dy=player.y+30-e.y-e.h/2,len=Math.hypot(dx,dy)||1;
   hazards.push({x:e.x+e.w/2,y:e.y+e.h/2,w:12,h:12,vx:dx/len*180,vy:dy/len*180,life:5,color:'#d9cd71'});e.spit=3;
  }
 }
 const sword={x:player.face>0?player.x+player.w:player.x-68,y:player.y+5,w:68,h:48};
 if(!shielding()&&player.attack>0&&e.lastSwing!==swingId&&overlap(sword,e)){e.lastSwing=swingId;e.hp-=moMode?e.max/2:1;stats.hits++;e.hit=.24;e.x+=player.face*(e.boss?24:48);burst(e.x+e.w/2,e.y+e.h/2,'#a8f06c');if(e.hp<=0){if(!e.boss){kills++;stats.slimes++;}else stats.bosses++;continue;}}
 if(e.hp>0&&e.hit===0&&overlap(player,e)){hurtPlayer(e.x);if(state==='over')break;}
 }
 enemies=enemies.filter(e=>e.hp>0);
 if(bossSpawned&&enemies.length===0)hazards=[];
 if(state==='playing')for(const h of hazards){if(h.delay>0){h.delay=Math.max(0,h.delay-dt);continue;}h.x+=h.vx*dt;h.y+=h.vy*dt;h.life-=dt;if(overlap(player,h)){hurtPlayer(h.x);h.life=0;}}
 hazards=hazards.filter(h=>h.life>0&&h.x+h.w>0&&h.x<960&&h.y+h.h>0&&h.y<480);
 if(state==='playing'&&bossSpawned&&enemies.length===0){stats.waves++;transition=2.5;status.textContent=wave===wavesForLevel()?`Level ${level} complete! Tougher slimes await in level ${level+1}.`:'Wave cleared! Recovering one heart…';}hud();
}
function rect(x,y,w,h,c){ctx.fillStyle=c;ctx.fillRect(x,y,w,h);}
function draw(){
 const sky=ctx.createLinearGradient(0,0,0,480);sky.addColorStop(0,'#211030');sky.addColorStop(1,region().sky);ctx.fillStyle=sky;ctx.fillRect(0,0,960,480);
 ctx.fillStyle='#d4ddad';ctx.beginPath();ctx.arc(765,88,34,0,Math.PI*2);ctx.fill();
 for(let i=0;i<8;i++){const x=i*145-30;rect(x,155+(i%3)*20,90,240,'#302039');for(let j=0;j<3;j++)rect(x+j*32,140+(i%3)*20,22,25,'#302039');rect(x+35,194+(i%3)*20,16,32,'#e8c778');}
 for(let i=0;i<20;i++){const x=i*55;rect(x,320,40,76,'#45304d');rect(x+3,324,34,4,'#927044');}
 // Royal standards hang above the keep walls.
 for(const bx of [105,395,685]){rect(bx,184,32,72,'#703d87');rect(bx,184,32,4,'#e8c778');ctx.fillStyle='#e8c778';ctx.font='24px Georgia';ctx.textAlign='center';ctx.fillText('♛',bx+16,225);}
 // Faint scratches on an ordinary background stone.
 ctx.save();ctx.translate(681,355);ctx.rotate(-.12);ctx.font='11px Georgia';ctx.fillStyle='#52665b';ctx.fillText('op',0,0);ctx.restore();
 rect(0,floor,960,84,'#302039');rect(0,floor,960,7,'#e8c778');for(let i=0;i<40;i++)rect(i*26,floor+18+(i%3)*17,12,3,'#594064');
 for(const e of enemies){ctx.fillStyle=e.hit>0?'#ecffc1':e.boss?bossTypes[e.kind].color:mobTypes[e.mob].color;ctx.beginPath();ctx.ellipse(e.x+e.w/2,e.y+e.h*.58,e.w/2,e.h*.43,0,Math.PI,Math.PI*2);ctx.lineTo(e.x+e.w,e.y+e.h);ctx.lineTo(e.x,e.y+e.h);ctx.fill();rect(e.x+e.w*.25,e.y+e.h*.5,6,9,'#152e2c');rect(e.x+e.w*.65,e.y+e.h*.5,6,9,'#152e2c');if(!e.boss){
 if(e.mob===1){rect(e.x-5,e.y+e.h-8,9,4,'#dced91');rect(e.x+e.w-3,e.y+e.h-8,9,4,'#dced91');}
 if(e.mob===2){rect(e.x+4,e.y-10,5,16,'#83e8c6');rect(e.x+e.w-9,e.y-10,5,16,'#83e8c6');}
 if(e.mob===3){rect(e.x+3,e.y+5,e.w-6,10,'#c1ccc4');rect(e.x+e.w/2-3,e.y,6,19,'#d5e4dc');}
 if(e.mob===4){rect(e.x+e.w/2-6,e.y+e.h-14,12,9,'#63552b');}
}if(e.boss){
 if(e.kind===0){rect(e.x+14,e.y-4,48,10,'#e8c565');for(let i=0;i<3;i++)rect(e.x+14+i*19,e.y-14,10,14,'#e8c565');}
 if(e.kind===1){for(const side of [0,1]){ctx.fillStyle='#ffe2ad';ctx.beginPath();ctx.moveTo(e.x+side*e.w,e.y+25);ctx.lineTo(e.x+side*e.w+(side?12:-12),e.y-12);ctx.lineTo(e.x+side*e.w+(side?-20:20),e.y+20);ctx.fill();}rect(e.x+18,e.y+48,40,10,'#63342f');}
 if(e.kind===2){ctx.fillStyle='#d7c5ff';ctx.beginPath();ctx.moveTo(e.x+38,e.y-18);ctx.lineTo(e.x+50,e.y+2);ctx.lineTo(e.x+38,e.y+22);ctx.lineTo(e.x+26,e.y+2);ctx.closePath();ctx.fill();}
 if(e.kind===3){for(let i=0;i<4;i++){ctx.fillStyle='#d0f7ff';ctx.beginPath();ctx.moveTo(e.x+8+i*18,e.y+12);ctx.lineTo(e.x+15+i*18,e.y-20);ctx.lineTo(e.x+22+i*18,e.y+12);ctx.fill();}}
 if(e.kind===4){for(let i=0;i<5;i++){ctx.fillStyle=i%2?'#edd29c':'#df98b1';ctx.beginPath();ctx.arc(e.x+8+i*15,e.y+4,11,0,Math.PI*2);ctx.fill();}rect(e.x+30,e.y+34,16,8,'#722f51');}
 ctx.textAlign='center';ctx.font='bold 13px system-ui';ctx.fillStyle='#fff2cd';ctx.fillText(bossTypes[e.kind].name,e.x+e.w/2,e.y-32);
 if(e.phase==='warn'){ctx.strokeStyle='#ffcf72';ctx.lineWidth=3;ctx.strokeRect(e.x-5,e.y-5,e.w+10,e.h+10);ctx.fillText(['STOMP!','CHARGE!','VOLLEY!','ICE FALL!','THORNS!'][e.kind],e.x+e.w/2,e.y-50);}
}if(e.max>1){rect(e.x,e.y-23,e.w,4,'#152e2c');rect(e.x,e.y-23,e.w*e.hp/e.max,4,'#c6eb91');}}
 if(player){ctx.save();if(player.inv>0&&Math.floor(time*14)%2)ctx.globalAlpha=.4;const x=player.x,y=player.y;rect(x+(player.face>0?-8:20),y+15,18,32,'#703d87');rect(x+4,y+18,23,25,'#adbac0');rect(x+2,y,27,22,'#d3dddd');rect(x+(player.face>0?15:2),y+8,13,6,'#263c45');rect(x+10,y-9,8,12,'#e8c778');rect(x+3,y+41,9,11,'#788e9b');rect(x+20,y+41,9,11,'#788e9b');rect(x+(player.face>0?0:21),y+22,12,20,'#e8c778');if(player.attack>0){ctx.strokeStyle='#e5f7bd';ctx.lineWidth=5;ctx.beginPath();ctx.arc(x+15,y+25,67,player.face>0?-.9:Math.PI-.9,player.face>0?.9:Math.PI+.9);ctx.stroke();rect(player.face>0?x+29:x-57,y+25,58,6,'#ecf5ef');}else{rect(x+(player.face>0?34:-9),y+8,5,31,'#e0ebe6');rect(x+(player.face>0?29:-14),y+33,15,5,'#e8c778');}ctx.restore();}
 if(player&&shielding()){
  ctx.save();ctx.strokeStyle='#9ee9ff';ctx.fillStyle='#75d8ef33';ctx.lineWidth=4;
  ctx.beginPath();ctx.ellipse(player.x+15,player.y+26,36,39,0,0,Math.PI*2);ctx.fill();ctx.stroke();
  const sx=player.face>0?player.x+27:player.x-13;
  rect(sx,player.y+13,17,31,'#527e99');rect(sx+3,player.y+16,11,25,'#baeaff');
  ctx.restore();
 }
 for(const h of hazards){
 if(h.delay>0){rect(h.x,floor-4,h.w,4,h.color);ctx.save();ctx.globalAlpha=.25;rect(h.x,h.y,h.w,h.h,h.color);ctx.restore();}
 else{rect(h.x,h.y,h.w,h.h,h.color);}
}
 for(const shot of projectiles){rect(shot.x,shot.y,shot.w,shot.h,'#72dfff');rect(shot.x+4,shot.y+3,10,4,'#ecffff');}
 for(const p of particles){ctx.globalAlpha=Math.max(0,p.life*2);rect(p.x,p.y,4,4,p.color);}ctx.globalAlpha=1;
 if(state!=='playing'){rect(0,0,960,480,'#190c29cc');ctx.textAlign='center';ctx.fillStyle='#fff3d6';ctx.font='bold 38px Georgia';ctx.fillText(state==='ready'?'The keep needs a knight':state==='paused'?'Paused':'Your watch has ended',480,216);ctx.font='18px system-ui';ctx.fillText(state==='ready'?'Start your adventure above':state==='paused'?'Press P to continue':'Press Restart for a new adventure',480,257);}
}
document.querySelector('#code').addEventListener('focus',()=>{keys.clear();if(state==='playing')togglePause();});
document.querySelector('#code-form').addEventListener('submit',e=>{
 e.preventDefault();const input=document.querySelector('#code'),message=document.querySelector('#code-message');
 const code=input.value.trim().toLowerCase();
 if(!['mo','op'].includes(code)){message.textContent='The inscription does not respond.';return;}
 if(code==='mo')moMode=true;
 if(code==='op'){opMode=true;document.querySelector('#fire').hidden=false;}
 keys.clear();
 if(!player||state==='over')reset();
 if(code==='mo'){player.hp=100;player.cool=0;}
 message.textContent=code==='mo'?'Empowered: 100 hearts, no cooldown, two-hit enemies!':'Projectiles unlocked! Press or hold J to fire.';

 input.value='';input.blur();document.activeElement.blur();
 if(state==='paused')togglePause();hud();
});
renderStats();renderLeaderboard();renderScore();function frame(now){const elapsed=last?Math.max(0,(now-last)/1000):0;const dt=Math.min(elapsed,.033);last=now;update(dt,elapsed);draw();requestAnimationFrame(frame);}requestAnimationFrame(frame);
</script>
</body>
</html>
```

