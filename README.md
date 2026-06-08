
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>Pulpería Don Pepe - MateKen</title>
<style>
*{box-sizing:border-box;font-family:Segoe UI,Tahoma,sans-serif}
body{margin:0;background:linear-gradient(135deg,#fff3e0,#ffe0b2);padding:12px}
.app{max-width:900px;margin:auto;background:#fff;border-radius:20px;padding:20px;box-shadow:0 10px 25px rgba(0,0,0,.15)}
h1{text-align:center;color:#ff6b00}
.top{display:flex;justify-content:space-between;gap:10px;flex-wrap:wrap;font-weight:700}
.avatar{font-size:70px;text-align:center;animation:float 2s infinite}
@keyframes float{50%{transform:translateY(-8px)}}
.card{background:#fff8f0;padding:16px;border-radius:15px;margin:10px 0}
.question{font-size:1.25rem;font-weight:bold}
.options button,.mainbtn{width:100%;padding:12px;border:none;border-radius:12px;margin:6px 0;cursor:pointer}
.options button{background:#ffe0b2}
.options button:hover{transform:scale(1.02)}
.mainbtn{background:#ff6b00;color:#fff;font-weight:bold}
.controls{display:flex;gap:8px}
.controls .mainbtn{flex:1}
.pista{display:inline-block;background:#87ceeb;padding:4px 8px;border-radius:10px;cursor:help}
.final{text-align:center;display:none}
.badge{font-size:2rem}
#confetti{font-size:2rem}
@media(max-width:600px){.avatar{font-size:55px}.question{font-size:1rem}}
</style>
</head>
<body>
<div class="app">
<h1>🏪 Pulpería Don Pepe</h1>

<div id="start">
<div class="avatar">🤖</div>
<button class="mainbtn" onclick="startGame()">▶ Empezar</button>
</div>

<div id="game" style="display:none">
<div class="top">
<div>🎯 Puntaje: <span id="score">0</span></div>
<div>⏱ <span id="time">0</span>s</div>
</div>

<div class="avatar" id="avatar">🤖</div>

<div class="card">
<div class="question" id="question"></div>
<div style="margin-top:8px">
<span class="pista" id="hint">☁️ Pista</span>
</div>
</div>

<div class="options" id="options"></div>

<div class="controls">
<button class="mainbtn" onclick="restart()">🔄 Reiniciar</button>
</div>
</div>

<div id="final" class="final">
<h2>🎉 Partida Finalizada</h2>
<div id="confetti">🎊🎉⭐🏆⭐🎉🎊</div>
<h1 id="percent"></h1>
<p id="summary"></p>
<div class="badge" id="badge"></div>
<button class="mainbtn" onclick="restart()">🔄 Reiniciar</button>
</div>
</div>

<script>
let questions=[],index=0,correct=0,timer=0,intv;

function rand(a,b){return Math.floor(Math.random()*(b-a+1))+a;}
function shuffle(a){return a.sort(()=>Math.random()-0.5);}

function buildBank(){
let bank=[];
for(let i=0;i<20;i++){
 let a=rand(2,9), b=rand(2,9);
 bank.push({q:`🥤 Compraste ${a} refrescos de C$${b*5}. ¿Cuánto pagaste?`,a:a*(b*5),h:"Multiplicación"});
 bank.push({q:`🍪 Tenías C$${a*50} y gastaste C$${b*10}. ¿Cuánto queda?`,a:a*50-b*10,h:"Resta"});
 bank.push({q:`🧃 Hay ${a*2} jugos y ${b*3} gaseosas. ¿Cuántas bebidas hay?`,a:a*2+b*3,h:"Suma"});
 bank.push({q:`🍫 Se reparten C$${a*b*10} entre ${a} personas. ¿Cuánto recibe cada una?`,a:(a*b*10)/a,h:"División"});
}
return shuffle(bank).slice(0,10);
}

function tone(f){
 const c=new (window.AudioContext||window.webkitAudioContext)();
 const o=c.createOscillator(); const g=c.createGain();
 o.connect(g); g.connect(c.destination); o.frequency.value=f;
 o.start(); g.gain.exponentialRampToValueAtTime(0.0001,c.currentTime+0.2);
 o.stop(c.currentTime+0.2);
}

function startGame(){
 document.getElementById("start").style.display="none";
 document.getElementById("game").style.display="block";
 questions=buildBank(); index=0; correct=0; timer=0;
 clearInterval(intv);
 intv=setInterval(()=>{timer++;time.textContent=timer;},1000);
 loadQ();
}

function loadQ(){
 let q=questions[index];
 question.textContent=`${index+1}/10 - ${q.q}`;
 hint.title="💡 "+q.h;
 let opts=[q.a,q.a+rand(5,20),Math.max(1,q.a-rand(3,15)),q.a+rand(21,40)];
 opts=shuffle([...new Set(opts)]).slice(0,4);
 options.innerHTML="";
 opts.forEach(v=>{
  let b=document.createElement("button");
  b.textContent="C$"+v;
  b.onclick=()=>answer(v);
  options.appendChild(b);
 });
}

function answer(v){
 let q=questions[index];
 if(v===q.a){correct++; score.textContent=Math.round(correct*10); avatar.textContent="🤖🎉"; tone(700);}
 else {avatar.textContent="🤖😕"; tone(250);}
 setTimeout(()=>avatar.textContent="🤖",500);
 index++;
 if(index>=10) finish(); else loadQ();
}

function finish(){
 clearInterval(intv);
 game.style.display="none";
 final.style.display="block";
 let pct=correct*10;
 percent.textContent=pct+"%";
 let badge="🥉 Aprendiz";
 if(pct>=80) badge="👑 Maestro MateKen";
 else if(pct>=60) badge="🏆 Experto";
 else if(pct>=40) badge="🥇 Matemático";
 summary.innerHTML=`✔ Correctas: ${correct}<br>✖ Incorrectas: ${10-correct}<br>⏱ Tiempo: ${timer}s`;
 document.getElementById("badge").textContent=badge;
}

function restart(){location.reload();}
</script>
</body>
</html>
