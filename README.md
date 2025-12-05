<!doctype html>
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
  --gap:10px;
  --cell-size:72px;
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
  padding:32px;
  overflow-x:hidden;
}

/* Матни болоӣ */
#title{
  font-size:48px;
  font-weight:800;
  background: linear-gradient(90deg, #ff0000, #ffff00, #00ff00, #00ffff, #0000ff, #ff00ff);
  background-size: 400% 100%;
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  animation: gradientShift 6s linear infinite;
  margin-bottom:20px;
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
  gap:20px;
  flex-direction:column;
  align-items:center;
}

.frame{
  background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(0,0,0,0.12));
  border:1px solid rgba(255,255,255,0.04);
  padding:18px;
  border-radius:14px;
  box-shadow: 0 8px 30px rgba(2,6,12,0.7), inset 0 1px 0 rgba(255,255,255,0.02);
}

.grid{
  display:grid;
  grid-template-columns:repeat(5, minmax(48px, 1fr));
  grid-auto-rows: minmax(48px, 1fr);
  gap:var(--gap);
  width: calc(var(--cell-size)*5 + var(--gap)*4);
  max-width: 560px;
}

.cell{
  background: linear-gradient(180deg, rgba(255,255,255,0.02), rgba(0,0,0,0.18));
  border-radius:10px;
  height:0;
  padding-bottom:100%;
  position:relative;
  overflow:hidden;
  cursor:pointer;
  display:flex;
  align-items:center;
  justify-content:center;
  transform-origin:center;
  transition: transform var(--speed) cubic-bezier(.2,.9,.3,1), box-shadow var(--speed) ease, opacity var(--speed) ease;
  box-shadow: 0 6px 18px rgba(2,6,12,0.6), inset 0 -6px 20px rgba(0,0,0,0.5);
  border:1px solid rgba(255,255,255,0.03);
}

.cell .content{
  font-size:34px;
  z-index:2;
  opacity:0;
  transition: opacity var(--speed) ease;
  position:absolute;
  top:50%;
  left:50%;
  transform: translate(-50%,-50%);
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

.outer-frame{
  padding:12px;
  border-radius:18px;
  background: linear-gradient(180deg, rgba(255,255,255,0.01), rgba(255,255,255,0.02));
  box-shadow: inset 0 1px 0 rgba(255,255,255,0.02);
}

.controls{
  margin-top:14px;
  display:flex;
  gap:10px;
  justify-content:center;
}

.btn{
  padding:10px 14px;
  border-radius:10px;
  border:0;
  cursor:pointer;
  font-weight:600;
  background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));
  color:var(--accent);
  border:1px solid rgba(255,217,90,0.18);
  box-shadow: 0 8px 20px rgba(0,0,0,0.6);
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

@media (max-width:520px){
  :root{--cell-size:56px}
  .grid{width:100%; grid-template-columns:repeat(5, 1fr)}
}
</style>
</head>
<body>
<div id="title">VZLOM MINES</div>

<div class="wrap">
  <div class="frame outer-frame">
    <div class="grid" id="grid" aria-label="5 by 5 grid"></div>
  </div>

  <div class="controls">
    <button class="btn" id="receiveBtn" disabled>получит сигнал</button>
    <button class="btn" id="newBtn">нови сигнал</button>
    <button class="btn" id="resetBtn" style="color:#d1e9ff; border-color: rgba(209,233,255,0.12)">сброс</button>
  </div>
</div>

<script>
const grid = document.getElementById('grid');
const receiveBtn = document.getElementById('receiveBtn');
const newBtn = document.getElementById('newBtn');
const resetBtn = document.getElementById('resetBtn');

const SPEED = 700;
const TOTAL = 25;

// Создаем ячейки
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
