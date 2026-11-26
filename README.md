# billion.html

A tiny, single-file Nyan Cat arcade game. Fly through space, collect stars, dodge cosmic blocks, and see how rare your run becomes. No image assets or build step required.

Save this as `index.html` (or open the repository's `README.md` in a browser) and play. Use **WASD / arrow keys** or **drag/touch** to steer. Collect stars for points; you have three lives.

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no">
<meta name="theme-color" content="#100b2e">
<title>Billion.io — Nyan Cat in Space</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap" rel="stylesheet">
<style>
:root{color-scheme:dark;--pink:#ff4fd8;--cyan:#62f5ff;--lime:#a4ff5f;--night:#100b2e}
*{box-sizing:border-box}
body{margin:0;min-height:100vh;padding:22px 14px 30px;background:radial-gradient(ellipse at 50% 0%,#30206d 0%,#17103c 42%,#09071b 100%);color:#fff;font-family:'Press Start 2P',monospace;font-size:10px;line-height:1.8;display:flex;justify-content:center;align-items:flex-start}
main{width:min(900px,100%)}
header{text-align:center;margin:0 0 16px}
h1{font-size:clamp(18px,4vw,30px);color:var(--pink);text-shadow:4px 4px #542ca1,7px 7px var(--cyan);margin:8px 0 12px;line-height:1.6}
.subtitle{color:#d8d2ff;margin:0;font-size:9px}
.cabinet{border:4px solid var(--lime);padding:10px;background:#0a0920;box-shadow:0 0 0 4px #0a0920,0 0 0 8px var(--pink),0 0 28px #ff4fd855}
.hud{display:flex;justify-content:space-between;align-items:center;gap:8px;flex-wrap:wrap;padding:4px 2px 10px;color:var(--cyan)}
.hud strong{color:#ffe66d}
#lives{color:var(--pink);letter-spacing:4px}
.frame{position:relative;overflow:hidden;border:3px solid #6f5bb6;background:#100b2e;line-height:0;touch-action:none}
canvas{display:block;width:100%;height:auto;aspect-ratio:8/5;image-rendering:pixelated;touch-action:none}
.overlay{position:absolute;inset:0;display:grid;place-items:center;text-align:center;padding:22px;background:#09071bd9;line-height:1.9}
.overlay[hidden]{display:none}
.card{max-width:570px}
.card h2{color:var(--cyan);font-size:clamp(13px,3vw,19px);line-height:1.8;text-shadow:3px 3px #6339a5}
.card p{color:#e8e3ff;font-size:9px;line-height:2.2}
button{font:inherit;color:#100b2e;background:var(--lime);border:0;padding:15px 18px;cursor:pointer;box-shadow:4px 4px #448a4b;line-height:1.7}
button:hover,button:focus-visible{background:#fff;outline:3px solid var(--pink);outline-offset:3px}
button:active{transform:translate(2px,2px);box-shadow:2px 2px #448a4b}
.instructions{text-align:center;color:#bbb2e8;font-size:8px;line-height:2.2;margin:16px 0 0}
.touch-controls{display:none;justify-content:center;gap:12px;margin:14px auto 0}
.touch-controls button{width:72px;height:58px;padding:0;font-size:16px;background:#251850;color:var(--cyan);border:2px solid var(--cyan);box-shadow:3px 3px #100b2e;touch-action:manipulation}
@media (pointer:coarse){.touch-controls{display:flex}}
@media(max-width:520px){body{padding:16px 12px 24px}.cabinet{padding:6px}.hud{font-size:8px}.instructions{font-size:7px}.card p{font-size:8px}}
</style>
</head>
<body>
<main>
  <header><h1>BILLION.IO</h1><p class="subtitle">NYAN CAT: COSMIC STAR RUN</p></header>
  <section class="cabinet" aria-label="Billion.io arcade game">
    <div class="hud" aria-live="polite"><span>SCORE <strong id="score">000000</strong></span><span>BEST <strong id="best">000000</strong></span><span id="lives" aria-label="3 lives">♥ ♥ ♥</span></div>
    <div class="frame">
      <canvas id="game" width="800" height="500" aria-label="Game field: steer Nyan Cat, collect stars, and avoid obstacles"></canvas>
      <div class="overlay" id="overlay"><div class="card"><h2 id="headline">READY, SPACE CAT?</h2><p id="message">Collect the stars. Dodge the cosmic blocks.<br>How rare can your run get?</p><button id="start" type="button">START GAME</button></div></div>
    </div>
    <div class="touch-controls" aria-label="Touch controls"><button type="button" data-dir="left" aria-label="Move left">◀</button><button type="button" data-dir="up" aria-label="Move up">▲</button><button type="button" data-dir="down" aria-label="Move down">▼</button><button type="button" data-dir="right" aria-label="Move right">▶</button></div>
  </section>
  <p class="instructions">MOVE: WASD / ARROW KEYS · COLLECT ✦ STARS · AVOID THE PURPLE BLOCKS<br>ON MOBILE: DRAG NYAN CAT OR USE THE ARROW BUTTONS</p>
</main>
<script>
(() => {
  const canvas = document.getElementById('game');
  const ctx = canvas.getContext('2d');
  const scoreEl = document.getElementById('score');
  const bestEl = document.getElementById('best');
  const livesEl = document.getElementById('lives');
  const overlay = document.getElementById('overlay');
  const headline = document.getElementById('headline');
  const message = document.getElementById('message');
  const startButton = document.getElementById('start');
  const W = canvas.width, H = canvas.height;
  const keys = new Set();
  const colors = ['#ff4d6d','#ff9f1c','#ffe66d','#7bff6b','#45d9ff','#a879ff'];
  let player, stars, rocks, particles, backdrop, score, lives, playing, lastTime, starTimer, rockTimer, invincible, dragging, best;
  try { best = Number(localStorage.getItem('billion-best')) || 0; } catch (_) { best = 0; }
  bestEl.textContent = pad(best);

  function pad(n){ return Math.max(0, Math.floor(n)).toString().padStart(6,'0'); }
  function reset(){
    score = 0; lives = 3; playing = true; lastTime = 0; starTimer = .4; rockTimer = 1.1; invincible = 0; dragging = false;
    player = {x:125,y:H/2,speed:300,blink:0}; stars = []; rocks = []; particles = [];
    backdrop = Array.from({length:95}, () => ({x:Math.random()*W,y:Math.random()*H,size:Math.random()*2+1,speed:15+Math.random()*55,phase:Math.random()*6}));
    scoreEl.textContent = pad(score); updateLives(); overlay.hidden = true;
  }
  function updateLives(){ livesEl.textContent = '♥ '.repeat(Math.max(0,lives)).trim() || '—'; livesEl.setAttribute('aria-label', lives+' lives'); }
  function start(){ reset(); requestAnimationFrame(loop); }
  function rand(min,max){ return min+Math.random()*(max-min); }
  function addStar(){ stars.push({x:W+24,y:rand(35,H-35),r:13,spin:rand(0,6),speed:rand(190,270)}); }
  function addRock(){ const size=rand(28,52); rocks.push({x:W+size,y:rand(35,H-35),w:size,h:size,speed:rand(205,280),phase:rand(0,6)}); }
  function burst(x,y,color,count=10){ for(let n=0;n<count;n++){const a=rand(0,Math.PI*2),v=rand(50,190);particles.push({x,y,vx:Math.cos(a)*v,vy:Math.sin(a)*v,life:rand(.3,.75),max:.75,color,size:rand(2,5)});} }

  function drawStar(x,y,r,spin,color='#ffe66d'){
    ctx.save();ctx.translate(x,y);ctx.rotate(spin);ctx.fillStyle=color;ctx.beginPath();
    for(let i=0;i<10;i++){const a=-Math.PI/2+i*Math.PI/5,rr=i%2?r*.42:r;ctx.lineTo(Math.cos(a)*rr,Math.sin(a)*rr);}
    ctx.closePath();ctx.fill();ctx.restore();
  }
  function drawCat(x,y,time){
    const px=6, top=Math.round(y-24), left=Math.round(x-36);
    // Rainbow trail, drawn in chunky pixel strips.
    for(let i=0;i<6;i++){
      ctx.fillStyle=colors[i];ctx.fillRect(left-105,top+8+i*7,72+Math.sin(time*10+i)*5,7);
    }
    // Poptart body.
    ctx.fillStyle='#ff8fcf';ctx.fillRect(left,top,48,48);
    ctx.fillStyle='#ffd6eb';ctx.fillRect(left+6,top+6,36,36);
    ctx.fillStyle='#ff75bc';[[10,11],[29,9],[17,25],[34,30],[8,34]].forEach(([a,b])=>ctx.fillRect(left+a,top+b,px,px));
    // Cat ears, face, paws, and tail.
    ctx.fillStyle='#d9d9d9';ctx.fillRect(left+2,top-6,12,12);ctx.fillRect(left+32,top-6,12,12);ctx.fillRect(left+8,top,32,34);
    ctx.fillStyle='#fff';ctx.fillRect(left+11,top+9,10,12);ctx.fillRect(left+28,top+9,10,12);
    ctx.fillStyle='#171329';ctx.fillRect(left+16,top+12,5,7);ctx.fillRect(left+31,top+12,5,7);ctx.fillRect(left+23,top+23,8,4);
    ctx.fillStyle='#999';ctx.fillRect(left+8,top+34,8,8);ctx.fillRect(left+32,top+34,8,8);ctx.fillRect(left-7,top+13,8,8);
    ctx.fillStyle='#fff';ctx.fillRect(left+3,top+6,5,5);ctx.fillRect(left+39,top+6,5,5);
  }
  function draw(time){
    ctx.fillStyle='#100b2e';ctx.fillRect(0,0,W,H);
    for(const s of backdrop){const twinkle=.45+.55*Math.sin(time*2+s.phase);ctx.globalAlpha=twinkle;ctx.fillStyle='#fff';ctx.fillRect(s.x,s.y,s.size,s.size);}
    ctx.globalAlpha=1;
    // Occasional distant planet.
    ctx.fillStyle='#30215d';ctx.beginPath();ctx.arc(670,105,47,0,Math.PI*2);ctx.fill();ctx.fillStyle='#433077';ctx.fillRect(632,94,77,8);ctx.fillRect(646,112,52,7);
    for(const s of stars)drawStar(s.x,s.y,s.r,s.spin);
    for(const r of rocks){ctx.fillStyle='#8c63d9';ctx.fillRect(r.x,r.y,r.w,r.h);ctx.fillStyle='#c9a7ff';ctx.fillRect(r.x+5,r.y+5,r.w-10,7);ctx.fillStyle='#50348c';ctx.fillRect(r.x+6,r.y+r.h-10,r.w-12,5);ctx.fillStyle='#e2d5ff';ctx.fillRect(r.x+r.w*.28,r.y+r.h*.35,5,5);ctx.fillRect(r.x+r.w*.65,r.y+r.h*.35,5,5);}
    for(const p of particles){ctx.globalAlpha=Math.max(0,p.life/p.max);ctx.fillStyle=p.color;ctx.fillRect(p.x,p.y,p.size,p.size);}ctx.globalAlpha=1;
    if(player && (invincible<=0 || Math.floor(time*12)%2===0))drawCat(player.x,player.y,time);
  }
  function overlap(a,b){return a.x+a.w>b.x && a.x<b.x+b.w && a.y+a.h>b.y && a.y<b.y+b.h;}
  function update(dt,time){
    for(const s of backdrop){s.x-=s.speed*dt;if(s.x<0){s.x=W;s.y=Math.random()*H;}}
    if(!playing)return;
    if(invincible>0)invincible-=dt;
    let dx=0,dy=0;
    if(keys.has('ArrowLeft')||keys.has('a'))dx--;if(keys.has('ArrowRight')||keys.has('d'))dx++;
    if(keys.has('ArrowUp')||keys.has('w'))dy--;if(keys.has('ArrowDown')||keys.has('s'))dy++;
    if(dx||dy){const mag=Math.hypot(dx,dy);player.x+=dx/mag*player.speed*dt;player.y+=dy/mag*player.speed*dt;}
    player.x=Math.max(54,Math.min(W-52,player.x));player.y=Math.max(42,Math.min(H-42,player.y));
    starTimer-=dt;rockTimer-=dt;
    if(starTimer<=0){addStar();starTimer=rand(.55,1.05);}
    if(rockTimer<=0){addRock();rockTimer=rand(.8,1.3)*Math.max(.58,1-score/5000);}
    const catBox={x:player.x-29,y:player.y-18,w:58,h:36};
    for(let i=stars.length-1;i>=0;i--){const s=stars[i];s.x-=s.speed*dt;s.spin+=dt*3;if(Math.hypot(s.x-player.x,s.y-player.y)<28){score+=100;scoreEl.textContent=pad(score);burst(s.x,s.y,'#ffe66d',8);stars.splice(i,1);}else if(s.x<-30)stars.splice(i,1);}
    for(let i=rocks.length-1;i>=0;i--){const r=rocks[i];r.x-=r.speed*dt;r.phase+=dt*4;if(invincible<=0&&overlap(catBox,{x:r.x+5,y:r.y+5,w:r.w-10,h:r.h-10})){lives--;updateLives();invincible=1.35;burst(player.x,player.y,'#ff4fd8',18);rocks.splice(i,1);if(lives<=0)finish();}else if(r.x+r.w<0)rocks.splice(i,1);}
    for(let i=particles.length-1;i>=0;i--){const p=particles[i];p.x+=p.vx*dt;p.y+=p.vy*dt;p.life-=dt;if(p.life<=0)particles.splice(i,1);}
    // A little survival score rewards keeping the run going.
    score+=dt*8;scoreEl.textContent=pad(score);
  }
  function rarity(n){if(n>=10000)return 'ONE IN A BILLION';if(n>=5000)return 'COSMIC LEGEND';if(n>=2500)return 'ULTRA RARE';if(n>=1000)return 'SUPER RARE';if(n>=400)return 'RARE';return 'STAR PILOT';}
  function finish(){playing=false;best=Math.max(best,Math.floor(score));bestEl.textContent=pad(best);try{localStorage.setItem('billion-best',String(best));}catch(_){}headline.textContent=rarity(score)+'!';message.innerHTML='RUN SCORE: '+pad(score)+'<br>NYAN CAT MADE IT '+Math.floor(score/8)+' LIGHT-YEARS.<br>READY FOR ANOTHER RUN?';startButton.textContent='PLAY AGAIN';overlay.hidden=false;}
  function loop(now){if(!playing)return;const time=now/1000,dt=Math.min(.035,(now-(lastTime||now))/1000);lastTime=now;update(dt,time);draw(time);if(playing)requestAnimationFrame(loop);}

  startButton.addEventListener('click',start);
  window.addEventListener('keydown',e=>{const key=e.key.length===1?e.key.toLowerCase():e.key;if(['ArrowUp','ArrowDown','ArrowLeft','ArrowRight',' '].includes(key))e.preventDefault();keys.add(key);if(!playing&&key===' '&&overlay.hidden===false)start();});
  window.addEventListener('keyup',e=>keys.delete(e.key.length===1?e.key.toLowerCase():e.key));
  window.addEventListener('blur',()=>keys.clear());
  function pointerPosition(e){const rect=canvas.getBoundingClientRect();return{x:(e.clientX-rect.left)*W/rect.width,y:(e.clientY-rect.top)*H/rect.height};}
  canvas.addEventListener('pointerdown',e=>{if(!playing)return;dragging=true;canvas.setPointerCapture(e.pointerId);const p=pointerPosition(e);player.x=p.x;player.y=p.y;});
  canvas.addEventListener('pointermove',e=>{if(playing&&dragging){const p=pointerPosition(e);player.x=p.x;player.y=p.y;}});
  canvas.addEventListener('pointerup',()=>dragging=false);canvas.addEventListener('pointercancel',()=>dragging=false);
  document.querySelectorAll('[data-dir]').forEach(button=>{
    const dir=button.dataset.dir;
    button.addEventListener('pointerdown',e=>{e.preventDefault();keys.add(({left:'ArrowLeft',right:'ArrowRight',up:'ArrowUp',down:'ArrowDown'})[dir]);});
    for(const event of ['pointerup','pointerleave','pointercancel'])button.addEventListener(event,()=>keys.delete(({left:'ArrowLeft',right:'ArrowRight',up:'ArrowUp',down:'ArrowDown'})[dir]));
  });
  draw(0);
})();
</script>
</body>
</html>
```
