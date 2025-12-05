<html lang="tg">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>VZLOM MINES — Сигнал</title>
<style>
:root{
  --bg:#0b0f12;
  --accent:#ffd95a;
  --speed:0.7s;
  --gap:8px;          /* Камтар барои мобайл */
  --cell-size:56px;    /* Камтар барои телефон */
}
*{box-sizing:border-box}
html,body{height:100%; margin:0; font-family:Inter, system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;}
body{
  background:radial-gradient(circle at 10% 10%, #0d1720 0%, var(--bg) 45%);
  color:#e6eef6;
  display:flex;
  flex-direction:column;
  align-items:center;
  justify-content:flex-start;
  padding:16px;
  overflow-x:hidden;
}

/* Матни болоӣ */
#title{
  font-size:32px;
  font-weight:800;
  background: linear-gradient(90deg, #ff0000, #ffff00, #00ff00, #00ffff, #0000ff, #ff00ff);
  background-size: 400% 100%;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  animation: gradientShift 6s linear infinite;
  margin-bottom:16px;
  text-align:center;
}

@keyframes gradientShift{
  0%{background-position:0% 50%;}
  50%{background-position:100% 50%;}
  100%{background-position:0% 50%;}
}

/* Grid ва cell-и ⭐ */
.wrap{
  display:flex;
  gap:16px;
  flex-direction:column;
  align-items:center;
  width:100%;
}

.frame{
  background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(0,0,0,0.12));
  border:1px solid rgba(255,255,255,0.04);
  padding:12px;
  border-radius:12px;
  box-shadow: 0 6px 20px rgba(2,6,12,0.7), inset 0 1px 0 rgba(255,255,255,0.02);
  width:100%;
  max-width:400px;
}

.grid{
  display:grid;
  grid-template-columns:repeat(5, 1fr);
  grid-auto-rows:1fr;
  gap:var(--gap);
  width:100%;
}

.cell{
  background: linear-gradient(180deg, rgba(255,255,255,0.02), rgba(0,0,0,0.18));
  border-radius:10px;
  position:relative;
  overflow:hidden;
  cursor:pointer;
  display:flex;
  align-items:center;
  justify-content:center;
  transform-origin:center;
  transition: transform var(--speed) cubic-bezier(.2,.9,.3,1), box-shadow var(--speed) ease, opacity var(--speed) ease;
  box-shadow: 0 4px 12px rgba(2,6,12,0.6), inset 0 -4px 12px rgba(0,0,0,0.5);
  border:1px solid rgba(255,255,255,0.03);
  aspect-ratio:1/1; /* square для мобильных */
}

.cell .content{
  font-size:28px;
  z-index:2;
  opacity:0;
  transition: opacity var(--speed) ease;
  color: rgba(255,217,90,1);
  filter: brightness(1.3);
}

.cell.open .content{
  opacity:1;
}

.cell:after{
  content:"";
  position:absolute;
  inset:0;
  pointer-events:none;
  background:linear-gradient(180deg, rgba(255,255,255,0.01), rgba(0,0,0,0.25));
  border-radius:10px;
  z-index:1;
}

.controls{
  margin-top:12px;
  display:flex;
  gap:8px;
  justify-content:center;
  flex-wrap:wrap;
}

.btn{
  padding:8px 12px;
  border-radius:8px;
  border:0;
  cursor:pointer;
  font-weight:600;
  background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));
  color:var(--accent);
  border:1px solid rgba(255,217,90,0.18);
  box-shadow: 0 6px 16px rgba(0,0,0,0.6);
  transition: transform 0.18s ease, box-shadow 0.18s ease, opacity 0.18s ease;
}
.btn:active{transform:translateY(2px)}

.btn[disabled]{
  opacity:0.38;
  cursor:not-allowed;
  box-shadow:none;
  border-color: rgba(255,255,255,0.03);
  color:#7a858f;
}

@media (max-width:400px){
  #title{font-size:24px;}
}
</style>
</head>
<body>
<div id="title">VZLOM MINES</div>

<div class="wrap">
  <div class="frame">
    <div class="grid" id="grid" aria-label="5 by 5 grid"></div>
  </div>

  <div class="controls">
    <button class="btn" id="receiveBtn" disabled>получит сигнал</button>
    <button class="btn" id="newBtn">нови сигнал</button>
    <button class="btn" id="resetBtn">сброс</button>
  </div>
</div>

<script>
const grid = document.getElementById('grid');
const receiveBtn = document.getElementById('receiveBtn');
const newBtn = document.getElementById('newBtn');
const resetBtn = document.getElementById('resetBtn');

const SPEED = 700;
const TOTAL = 25;

for(let i=0;i<TOTAL;i++){
  const btn = document.createElement('button');
  btn.className = 'cell';
  btn.type = 'button';
  btn.dataset.index = i;

  const content = document.createElement('div');
  content.className = 'content';
  content.innerHTML = '&#11088;';
  btn.appendChild(content);

  grid.appendChild(btn);
}

function getClosedCells(){
  return Array.from(grid.children).filter(c=>!c.classList.contains('open'));
}

function openRandom4(){
  const closed = getClosedCells();
  if(closed.length===0) return;
  const picks = [];
  const n = Math.min(4, closed.length);
  while(picks.length < n){
    const idx = Math.floor(Math.random()*closed.length);
    const el = closed[idx];
    if(!picks.includes(el)) picks.push(el);
  }
  picks.forEach((el,i)=>{
    setTimeout(()=>{
      el.classList.add('open');
    }, i*120);
  });
}

function resetAll(){
  Array.from(grid.children).forEach(c=>{
    c.classList.remove('open');
  });
  receiveBtn.disabled = true;
  newBtn.disabled = false;
}

newBtn.addEventListener('click', ()=>{
  newBtn.disabled = true;
  receiveBtn.disabled = false;
});

receiveBtn.addEventListener('click', ()=>{
  openRandom4();
  receiveBtn.disabled = true;
  newBtn.disabled = false;
});

resetBtn.addEventListener('click', resetAll);
resetAll();
document.documentElement.style.setProperty('--speed', '0.7s');
</script>
</body>
</html>
