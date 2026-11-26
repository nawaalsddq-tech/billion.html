# billion.html
krillion but better
<!DOCTYPE html>
<html><head><meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>One in a Billi-on</title>
<link href="https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap" rel="stylesheet">
<style>
body{background:#0b0b1e;color:#7CFC00;font-family:'Press Start 2P',monospace;margin:0;padding:16px;
 display:flex;justify-content:center;line-height:1.7;font-size:12px}
#box{max-width:560px;width:100%;border:4px solid #7CFC00;padding:18px;box-shadow:0 0 0 4px #0b0b1e,0 0 0 8px #ff4fd8}
h1{color:#ff4fd8;font-size:18px;text-align:center;text-shadow:3px 3px #00e5ff}
.q{color:#fff;margin:14px 0}
button{display:block;width:100%;margin:8px 0;padding:12px;background:#0b0b1e;color:#00e5ff;
 border:3px solid #00e5ff;font-family:inherit;font-size:11px;cursor:pointer;text-align:left}
button:hover{background:#00e5ff;color:#0b0b1e}
img{image-rendering:pixelated;max-width:100%;max-height:260px;display:block;margin:12px auto;border:4px solid #ff4fd8}
.big{color:#ffe600;font-size:16px;text-align:center}
.tag{color:#ff4fd8;text-align:center}
#bar{height:10px;background:#222;margin-bottom:8px}#fill{height:100%;background:#ff4fd8;width:0}
</style></head><body><div id="box"></div>
<script>
// ===== EDIT YOUR MEMES HERE (rarest first). Files sit next to this html. =====
const TIERS=[
 {min:1e9, name:"ONE IN A BILLI-ON", cat:"Nyan Cat",      img:"cat1.jpg", note:"Legendary. Mythic. Rainbow-tailed."},
 {min:1e8, name:"ULTRA RARE",        cat:"Maxwell",       img:"cat2.jpg", note:"You are spinning at cosmic speed."},
 {min:1e7, name:"SUPER RARE",        cat:"Huh Cat",       img:"cat3.jpg", note:"Even the cat is confused by you."},
 {min:1e6, name:"RARE",              cat:"Keyboard Cat",  img:"cat4.jpg", note:"Play 'em off."},
 {min:1e4, name:"UNCOMMON",          cat:"Grumpy Cat",    img:"cat5.jpg", note:"Meh. Still above average."},
 {min:0,   name:"COMMON",            cat:"Longcat",       img:"cat6.jpg", note:"Very normal. Very long."}
];
const TITLE="ONE IN A BILLI-ON", FOR_NAME="Friend";
// p = rough share of people who'd pick it. Smaller = rarer.
const QS=[
 {q:"MATH: Pick a favorite number.",o:[["7",.28],["3",.2],["42",.08],["1729",.002]]},
 {q:"MATH: Which constant do you love most?",o:[["pi",.45],["e",.2],["golden ratio",.05],["Euler-Mascheroni γ",.003]]},
 {q:"CS: Pick a data structure.",o:[["array",.4],["hash map",.3],["trie",.04],["bloom filter",.008]]},
 {q:"CS: Pick a language.",o:[["Python",.4],["Java",.2],["Haskell",.02],["COBOL",.006]]},
 {q:"US: Pick a state.",o:[["California",.2],["Texas",.15],["Delaware",.02],["North Dakota",.01]]},
 {q:"INDIA: Pick a city.",o:[["Mumbai",.3],["Delhi",.25],["Pondicherry",.02],["Shillong",.008]]},
 {q:"FOOD: Pick a dish.",o:[["pizza",.35],["biryani",.2],["pani puri",.08],["haggis",.004]]},
 {q:"FOOD: Pick a pizza topping.",o:[["pepperoni",.4],["paneer",.06],["pineapple",.1],["anchovy",.01]]},
 {q:"GEOGRAPHY: Pick a country.",o:[["Japan",.2],["Brazil",.12],["Bhutan",.012],["Tuvalu",.0008]]},
 {q:"GEOGRAPHY: Pick a river.",o:[["Nile",.3],["Ganges",.15],["Mekong",.04],["Yenisei",.003]]}
];
let i=0,odds=1,box=document.getElementById("box");
const fmt=n=>n>=1e9?(n/1e9).toFixed(1).replace(/\.0$/,"")+" billion":n>=1e6?(n/1e6).toFixed(1).replace(/\.0$/,"")+" million":Math.round(n).toLocaleString();
function show(){
 if(i>=QS.length)return end();
 const s=QS[i];
 box.innerHTML=`<h1>${TITLE}</h1><div id="bar"><div id="fill" style="width:${i/QS.length*100}%"></div></div>
 <div>Q ${i+1}/${QS.length}</div><div class="q">${s.q}</div>`;
 s.o.slice().sort(()=>Math.random()-.5).forEach(([t,p])=>{
  const b=document.createElement("button");b.textContent="> "+t;
  b.onclick=()=>{odds*=p;i++;show()};box.appendChild(b)});
}
function end(){
 const n=1/odds,t=TIERS.find(t=>n>=t.min);
 box.innerHTML=`<h1>${TITLE}</h1><p class="tag">${FOR_NAME}, YOUR ODDS:</p>
 <p class="big">1 IN ${fmt(n)}</p><p class="tag">${t.name}</p>
 <img src="${t.img}" alt="${t.cat}" onerror="this.style.display='none'">
 <p class="big">YOU ARE: ${t.cat.toUpperCase()}</p><p class="tag">${t.note}</p>
 <button onclick="i=0;odds=1;show()">> PLAY AGAIN</button>`;
}
show();
</script></body></html>
