<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>Flip &amp; Learn</title>
<style>
:root{--bg:#eef1fb;--surface:#fff;--ink:#16193a;--muted:#5b6088;--line:#d3d8f0;--accent:#3b4cca;--accent-ink:#fff;--front:#3b4cca;--back:#ffcf33;--back-ink:#16193a;--ok:#1b7f4b;--bad:#c23a3a;
box-sizing:border-box;padding-top:env(safe-area-inset-top,0px);padding-bottom:env(safe-area-inset-bottom,0px)}
@media (prefers-color-scheme:dark){:root:not([data-theme="light"]){--bg:#10122b;--surface:#1a1d40;--ink:#eceeff;--muted:#a3a8d6;--line:#33376b;--accent:#8e9bff;--accent-ink:#10122b;--front:#2d38a6;--ok:#58d394;--bad:#ff8585}}
:root[data-theme="dark"]{--bg:#10122b;--surface:#1a1d40;--ink:#eceeff;--muted:#a3a8d6;--line:#33376b;--accent:#8e9bff;--accent-ink:#10122b;--front:#2d38a6;--ok:#58d394;--bad:#ff8585}
html{scroll-padding-top:env(safe-area-inset-top,0px)}
*{box-sizing:border-box}
body{margin:0;background:var(--bg);color:var(--ink);font:16px/1.5 "Trebuchet MS","Segoe UI",system-ui,sans-serif}
main{max-width:720px;margin:0 auto;padding:20px 16px 60px}
header{display:flex;justify-content:space-between;align-items:center;gap:10px;margin-bottom:16px}
h1{margin:0;font-size:2rem;letter-spacing:-.02em}
h2{font-size:1.15rem;margin:0 0 10px}
button,input,textarea{font:inherit}
button:focus-visible,input:focus-visible,textarea:focus-visible{outline:3px solid var(--accent);outline-offset:2px}
.btn{background:var(--accent);color:var(--accent-ink);border:0;padding:10px 18px;border-radius:8px;cursor:pointer;font-weight:700}
.btn.ghost{background:none;color:var(--ink);border:1px solid var(--line);font-weight:400}
.btn.ok{background:var(--ok);color:#fff}.btn.bad{background:var(--bad);color:#fff}
.btn:disabled{opacity:.4;cursor:default}
.row{display:flex;gap:8px;flex-wrap:wrap;align-items:center}
.center{justify-content:center}
.stats{display:flex;gap:16px;color:var(--muted);margin-bottom:10px}
.stats b{color:var(--ink)}
.bar{height:8px;background:var(--line);border-radius:4px;overflow:hidden;margin-bottom:18px}
.bar i{display:block;height:100%;width:0;background:var(--ok);transition:width .3s}
.scene{perspective:1000px;margin-bottom:16px}
.card{position:relative;height:260px;cursor:pointer;transform-style:preserve-3d;transition:transform .45s;border:0;width:100%;padding:0;background:none}
.card.flip{transform:rotateY(180deg)}
.face{position:absolute;inset:0;backface-visibility:hidden;display:flex;flex-direction:column;align-items:center;justify-content:center;padding:24px;text-align:center;border-radius:16px;font-size:1.6rem;font-weight:700;line-height:1.3}
.face small{font-size:.8rem;font-weight:400;opacity:.8;margin-top:12px}
.f{background:var(--front);color:#fff}
.b{background:var(--back);color:var(--back-ink);transform:rotateY(180deg)}
.panel{background:var(--surface);border:1px solid var(--line);border-radius:12px;padding:18px;margin-top:22px}
input,textarea{width:100%;padding:10px;border:1px solid var(--line);border-radius:8px;background:var(--bg);color:var(--ink)}
textarea{min-height:64px;resize:vertical}
label{display:block;margin-bottom:10px;color:var(--muted);font-size:.9rem}
ul{list-style:none;margin:12px 0 0;padding:0}
li{display:flex;gap:10px;align-items:center;padding:10px 0;border-top:1px solid var(--line)}
li div{flex:1;min-width:0}
li span{display:block;color:var(--muted);font-size:.9rem}
li.known strong::after{content:" ✓";color:var(--ok)}
.x{background:none;border:0;color:var(--muted);font-size:1.4rem;cursor:pointer}
.done{text-align:center;padding:40px 10px}
.done b{font-size:2.4rem;display:block}
@media (prefers-reduced-motion:reduce){.card{transition:none}}
</style>
</head>
<body>
<main>
<header>
  <h1>Flip &amp; Learn</h1>
  <button class="btn ghost" id="theme">Theme</button>
</header>
 
<div class="stats">
  <div>Cards: <b id="sTotal">0</b></div>
  <div>Known: <b id="sKnown">0</b></div>
  <div>Left in round: <b id="sLeft">0</b></div>
</div>
<div class="bar"><i id="prog"></i></div>
 
<div id="study">
  <div class="scene">
    <button class="card" id="card" aria-label="Flip card">
      <div class="face f"><span id="front"></span><small>Tap to see the answer</small></div>
      <div class="face b"><span id="back"></span><small>Did you know it?</small></div>
    </button>
  </div>
  <div class="row center">
    <button class="btn bad" id="again">Study again</button>
    <button class="btn ok" id="got">I knew it</button>
  </div>
  <div class="row center" style="margin-top:12px">
    <button class="btn ghost" id="shuffle">Shuffle</button>
    <button class="btn ghost" id="restart">Start new round</button>
  </div>
</div>
 
<div class="panel">
  <h2>Add a card</h2>
  <label>Question or term<textarea id="q"></textarea></label>
  <label>Answer<textarea id="a"></textarea></label>
  <button class="btn" id="add">Add card</button>
</div>
 
<div class="panel">
  <h2>All cards</h2>
  <ul id="list"></ul>
</div>
</main>
 
<script>
(function(){
  var $=function(id){return document.getElementById(id)};
  function load(k,d){try{var v=localStorage.getItem(k);return v===null?d:JSON.parse(v)}catch(e){return d}}
  function save(k,v){try{localStorage.setItem(k,JSON.stringify(v))}catch(e){}}
  function esc(s){var d=document.createElement('div');d.textContent=s;return d.innerHTML}
 
  var cards=load('fl_cards',null)||[
    {q:'What is the powerhouse of the cell?',a:'The mitochondrion',k:false},
    {q:'What is the capital of Japan?',a:'Tokyo',k:false},
    {q:'Solve: 12 × 12',a:'144',k:false},
    {q:'Who wrote “Noli Me Tángere”?',a:'José Rizal',k:false}
  ];
  var queue=[];
 
  function newRound(){
    queue=[];
    cards.forEach(function(c,i){if(!c.k)queue.push(i)});
    show();
  }
  function persist(){save('fl_cards',cards)}
  function stats(){
    var known=cards.filter(function(c){return c.k}).length;
    $('sTotal').textContent=cards.length;$('sKnown').textContent=known;$('sLeft').textContent=queue.length;
    $('prog').style.width=(cards.length?known/cards.length*100:0)+'%';
  }
  function show(){
    $('card').classList.remove('flip');
    var box=$('study');
    stats();renderList();
    if(!cards.length){
      box.innerHTML='<div class="done">No cards yet. Add your first card below.</div>';return;
    }
    if(!queue.length){
      box.innerHTML='<div class="done"><b>All done!</b>You know every card in this deck.<br><br><button class="btn" id="again2">Study everything again</button></div>';
      $('again2').onclick=function(){cards.forEach(function(c){c.k=false});persist();location.hash='';rebuild()};
      return;
    }
    var c=cards[queue[0]];
    $('front').textContent=c.q;$('back').textContent=c.a;
  }
  function rebuild(){
    $('study').innerHTML='<div class="scene"><button class="card" id="card" aria-label="Flip card"><div class="face f"><span id="front"></span><small>Tap to see the answer</small></div><div class="face b"><span id="back"></span><small>Did you know it?</small></div></button></div><div class="row center"><button class="btn bad" id="again">Study again</button><button class="btn ok" id="got">I knew it</button></div><div class="row center" style="margin-top:12px"><button class="btn ghost" id="shuffle">Shuffle</button><button class="btn ghost" id="restart">Start new round</button></div>';
    bind();newRound();
  }
  function bind(){
    $('card').onclick=function(){this.classList.toggle('flip')};
    $('got').onclick=function(){if(!queue.length)return;cards[queue.shift()].k=true;persist();show()};
    $('again').onclick=function(){if(!queue.length)return;queue.push(queue.shift());show()};
    $('shuffle').onclick=function(){
      for(var i=queue.length-1;i>0;i--){var j=Math.floor(Math.random()*(i+1)),t=queue[i];queue[i]=queue[j];queue[j]=t}
      show();
    };
    $('restart').onclick=function(){cards.forEach(function(c){c.k=false});persist();newRound()};
  }
  function renderList(){
    $('list').innerHTML=cards.length?cards.map(function(c,i){
      return '<li class="'+(c.k?'known':'')+'"><div><strong>'+esc(c.q)+'</strong><span>'+esc(c.a)+'</span></div><button class="x" data-del="'+i+'" aria-label="Delete card">&times;</button></li>';
    }).join(''):'<li style="border:0;color:var(--muted)">Your deck is empty.</li>';
  }
  $('list').addEventListener('click',function(e){
    var d=e.target.dataset.del;if(d===undefined)return;
    cards.splice(d,1);persist();
    if($('card'))newRound();else rebuild();
  });
  $('add').onclick=function(){
    var q=$('q').value.trim(),a=$('a').value.trim();
    if(!q||!a){(q?$('a'):$('q')).focus();return}
    cards.push({q:q,a:a,k:false});persist();
    $('q').value='';$('a').value='';
    if($('card'))newRound();else rebuild();
  };
  $('theme').onclick=function(){
    var r=document.documentElement,dark=r.dataset.theme?r.dataset.theme==='dark':matchMedia('(prefers-color-scheme:dark)').matches;
    r.dataset.theme=dark?'light':'dark';
  };
  bind();newRound();
})();
</script>
</body>
</html>
 