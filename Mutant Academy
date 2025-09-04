<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="utf-8"/>
<meta name="viewport" content="width=device-width,initial-scale=1"/>
<title>Mutant Academy — Danger Room</title>
<style>
:root{
  --bg:#06070a; --panel:#0f1720; --muted:#9fb0bd; --accent:#6ee7b7; --danger:#ff6b6b; --gold:#ffd166; --text:#ecf7f6;
}
*{box-sizing:border-box}
body{margin:0;font-family:Inter,system-ui,Arial;background:linear-gradient(180deg,#030407,#071121);color:var(--text);display:flex;justify-content:center;padding:12px}
.container{width:100%;max-width:520px;background:var(--panel);border-radius:12px;padding:12px;border:1px solid rgba(255,255,255,.04);box-shadow:0 10px 30px rgba(0,0,0,.6)}
header{text-align:center}
h1{margin:8px 0;color:var(--accent);font-size:20px}
.small{font-size:13px;color:var(--muted)}
.row{display:flex;gap:8px;flex-wrap:wrap}
.col{flex:1}
.panel{background:rgba(255,255,255,0.02);padding:10px;border-radius:10px;margin-top:8px}
button{background:#0b1116;border:1px solid rgba(255,255,255,.04);color:var(--text);padding:10px 12px;border-radius:10px;font-weight:700;cursor:pointer}
button:disabled{opacity:.45;cursor:not-allowed}
.grid{display:grid;grid-template-columns:1fr 1fr;gap:8px}
.avatar{font-size:48px;width:74px;height:74px;border-radius:10px;display:flex;align-items:center;justify-content:center;background:rgba(255,255,255,0.02)}
.stat{font-size:13px;color:var(--muted)}
.hpbar{height:12px;background:#121416;border-radius:10px;overflow:hidden;border:1px solid rgba(255,255,255,.03)}
.fill{height:100%;transition:width .35s ease;background:linear-gradient(90deg,#46d0b9,#1aa08c)}
.fill.enemy{background:linear-gradient(90deg,#ff7b7b,#ff4d4d)}
#log{height:160px;overflow:auto;padding:6px;border-radius:8px;background:rgba(255,255,255,0.01);font-size:13px;color:var(--muted)}
.floating{position:fixed;pointer-events:none;font-weight:900;text-shadow:0 6px 16px rgba(0,0,0,.6);animation:floatUp 850ms ease-out forwards}
@keyframes floatUp{from{opacity:1;transform:translate(-50%,0) scale(1)}to{opacity:0;transform:translate(-50%,-60px) scale(1.02)}}
.controls{display:flex;gap:8px;flex-wrap:wrap;margin-top:8px}
.note{font-size:12px;color:var(--muted);margin-top:8px}
.footer{display:flex;justify-content:center;margin-top:10px}
.success{color:var(--accent)} .fail{color:var(--danger)}
.smallpad{padding:6px}
.hidden{display:none}
@media(max-width:420px){ .grid{grid-template-columns:1fr} .avatar{font-size:40px} }
</style>
</head>
<body>
<div class="container">
  <header>
    <h1>🧪 Mutant Academy — Danger Room</h1>
    <div class="small">Deviens élève X-Men : choisis ton pouvoir, passe 3 épreuves, puis affronte un vilain simulé.</div>
  </header>

  <!-- START -->
  <section id="startPanel" class="panel">
    <div class="smallpad">
      <div class="small">Choisis la difficulté :</div>
      <div class="row" style="margin-top:8px">
        <button onclick="start('facile')">😃 Facile</button>
        <button onclick="start('normal')">😐 Normal</button>
        <button onclick="start('difficile')">😈 Difficile</button>
      </div>
      <hr style="border:none;border-top:1px solid rgba(255,255,255,.03);margin:10px 0">
      <div class="small">Choisis ton pouvoir :</div>
      <div class="grid" style="margin-top:8px" id="powerChoices">
        <!-- injected -->
      </div>
      <div class="note">Chaque pouvoir est à <b>usage unique</b> durant les épreuves + combat final.</div>
    </div>
  </section>

  <!-- GAME -->
  <section id="gamePanel" class="panel hidden">
    <div class="row">
      <div style="max-width:180px">
        <div style="display:flex;gap:8px;align-items:center;">
          <div class="avatar" id="playerEmoji">🦸</div>
          <div>
            <div class="stat"><b id="playerName">Élève</b></div>
            <div id="playerPVtxt" class="stat">PV: 0 / 0</div>
            <div class="hpbar" style="margin-top:6px"><div id="playerHP" class="fill" style="width:100%"></div></div>
          </div>
        </div>
        <div class="note" id="powerNote" style="margin-top:8px"></div>
      </div>

      <div>
        <div class="small"><b>Épreuve</b> <span id="stageTxt">— Entraînement</span></div>
        <div id="challengeBox" class="smallpad" style="margin-top:8px">
          <!-- dynamic -->
        </div>
        <div class="controls" id="actionButtons"></div>
      </div>
    </div>

    <div class="panel" style="margin-top:10px">
      <div class="small"><b>Historique</b></div>
      <div id="log"></div>
    </div>
    <div class="footer" style="margin-top:8px">
      <div class="small">Score: <b id="score">0</b></div>
    </div>
  </section>

  <!-- END -->
  <section id="endPanel" class="panel hidden" style="text-align:center">
    <h2 id="endTitle"></h2>
    <p id="endText" class="small"></p>
    <div style="margin-top:10px"><button onclick="location.reload()">🔄 Rejouer</button></div>
  </section>
</div>

<script>
/* ====== Data ====== */
const POWERS = {
  telekinesis: {
    id:'telekinesis', name:'Télékinésie', emoji:'🧠',
    desc:'Peut manipuler l’adversaire : +chance d\'esquive 1 tour ou pousser objets (bonus utile).',
    effect: (ctx)=>{ ctx.player.hasPower=false; ctx.log('🔮 Télékinésie activée : adversaire désorienté (esquive +30%)'); ctx.player.evadeBuff=0.3; }
  },
  claws: {
    id:'claws', name:'Griffes', emoji:'🗡️',
    desc:'Attaque tranchante : dégâts élevés sur une attaque (usage unique).',
    effect:(ctx)=>{ ctx.player.hasPower=false; ctx.log('🗡️ Griffes utilisées : attaque massive prochaine'); ctx.player.powerNext='claws'; }
  },
  speed: {
    id:'speed', name:'Vitesse', emoji:'⚡',
    desc:'Donne une action supplémentaire (une fois) pendant un combat ou une épreuve.',
    effect:(ctx)=>{ ctx.player.hasPower=false; ctx.log('⚡ Vitesse activée : action supplémentaire disponible'); ctx.player.extraAction=true; }
  },
  ice: {
    id:'ice', name:'Glace', emoji:'❄️',
    desc:'Peut geler l’adversaire 1 tour (empêche sa riposte).',
    effect:(ctx)=>{ ctx.player.hasPower=false; ctx.log('❄️ Glace activée : prochaine riposte ennemie gelée'); ctx.enemy.frozenNext=true; }
  },
  pyro: {
    id:'pyro', name:'Pyro', emoji:'🔥',
    desc:'Attaque à zone : inflige dégâts sur plusieurs tours (brûlure).',
    effect:(ctx)=>{ ctx.player.hasPower=false; ctx.log('🔥 Pyro activée : brûlure infligée (dégâts sur 2 tours)'); ctx.enemy.burnNext=2; }
  }
};

const VILLAINS = [
  { id:'magneto', name:'Magneto', emoji:'🧲', pvBase:28, dmg:[3,6], special:'peut réduire efficacité armes (aléa)' },
  { id:'mystique', name:'Mystique', emoji:'🌀', pvBase:22, dmg:[2,5], special:'peut copier une action (aléa)' },
  { id:'sentinel', name:'Sentinel', emoji:'🤖', pvBase:34, dmg:[4,7], special:'fortes attaques mais lente' }
];

let state = {
  difficulty:'normal',
  chosenPower:null,
  player:{ name:'Élève', emoji:'🦸', pv:0, max:0, hasPower:false, powerNext:null, extraAction:false, evadeBuff:0 },
  enemy:null,
  score:0,
  stage:0, // 0..2 training, 3 final
  challenges:[], // array of challenge objects
  history:[]
};

/* ===== Utilities ===== */
const $ = id => document.getElementById(id);
function rand(min,max){ return Math.floor(Math.random()*(max-min+1))+min; }
function log(msg){ const d=$('log'); const el=document.createElement('div'); el.innerHTML=msg; d.appendChild(el); d.scrollTop=d.scrollHeight; state.history.push(msg); }
function spawnFloating(el,txt,color='white'){ if(!el) return; const r=el.getBoundingClientRect(); const f=document.createElement('div'); f.className='floating'; f.textContent=txt; f.style.left=(r.left+r.width/2)+'px'; f.style.top=(r.top+10)+'px'; f.style.color=color; document.body.appendChild(f); setTimeout(()=>f.remove(),900); }
function updatePlayerUI(){ $('playerPVtxt').textContent = `PV: ${state.player.pv} / ${state.player.max}`; $('playerHP').style.width = Math.max(0, (state.player.pv/state.player.max*100))+'%'; $('playerEmoji').textContent = state.player.emoji; $('playerName').textContent = state.player.name; $('score').textContent = state.score; }
function endGame(title,text){ $('gamePanel').classList.add('hidden'); $('endPanel').classList.remove('hidden'); $('endTitle').textContent=title; $('endText').innerHTML=text; }

/* ===== Start UI: render power choices ===== */
function renderPowers(){
  const target = $('powerChoices'); target.innerHTML='';
  Object.values(POWERS).forEach(p=>{
    const btn = document.createElement('button');
    btn.innerHTML = `${p.emoji} <div style="font-weight:700">${p.name}</div><div class="small" style="font-weight:400">${p.desc}</div>`;
    btn.style.textAlign='left';
    btn.onclick = ()=>{ selectPower(p.id); };
    target.appendChild(btn);
  });
}
renderPowers();

/* ===== Start game ===== */
function start(diff){
  state.difficulty = diff;
  // default player PV by difficulty
  const basePV = diff==='facile'?40:(diff==='difficile'?28:34);
  state.player = { name:'Novice', emoji:'🦸', pv:basePV, max:basePV, hasPower:false, powerNext:null, extraAction:false, evadeBuff:0 };
  state.chosenPower = null; state.score = 0; state.history=[]; state.stage=0;
  $('startPanel').classList.add('hidden'); $('gamePanel').classList.remove('hidden'); $('endPanel').classList.add('hidden');
  $('challengeBox').innerHTML = '<div class="small">Choisis d’abord ton pouvoir à gauche puis clique sur <b>Commencer l’entraînement</b>.</div>';
  updatePlayerUI();
  log(`🔰 Début de la session — difficulté : ${diff.toUpperCase()}`);
  // show a "commencer" button
  const actions = $('actionButtons'); actions.innerHTML='';
  const startBtn = document.createElement('button'); startBtn.textContent='▶️ Commencer l\'entraînement'; startBtn.onclick = ()=>{ if(!state.chosenPower) return alert('Choisis un pouvoir avant de commencer'); beginTraining(); };
  actions.appendChild(startBtn);
}

/* ===== Power select ===== */
function selectPower(pid){
  const p = POWERS[pid];
  state.chosenPower = p;
  state.player.emoji = p.emoji;
  state.player.hasPower = true;
  $('powerNote').innerHTML = `<b>${p.emoji} ${p.name}</b> sélectionné — ${p.desc}`;
  updatePlayerUI();
  log(`✨ Pouvoir choisi : ${p.name}`);
}

/* ===== Build training challenges (3) ===== */
function beginTraining(){
  // create three random challenge types
  const pool = ['combat','rescue','puzzle'];
  state.challenges = [ sample(pool), sample(pool), sample(pool) ];
  state.stage = 0;
  log('🏋️‍♂️ Entraînement : 3 épreuves à suivre.');
  nextChallenge();
}
function sample(arr){ return arr[Math.floor(Math.random()*arr.length)]; }

/* ===== Challenge dispatcher ===== */
function nextChallenge(){
  if(state.stage >= 3){
    // move to final
    prepareFinal();
    return;
  }
  const ch = state.challenges[state.stage];
  $('stageTxt').textContent = `— Entraînement (${state.stage+1}/3)`;
  if(ch==='combat') startCombatBot();
  else if(ch==='rescue') startRescue();
  else startPuzzle();
}

/* ===== Training: Combat Bot ===== */
function startCombatBot(){
  // simple 3-turn combat, bot has pv and dmg scaled by difficulty
  const baseBotPV = state.difficulty==='facile'?10:(state.difficulty==='difficile'?16:12);
  const bot = { name:'Robot d\'entraînement', emoji:'🤖', pv:baseBotPV, max:baseBotPV, dmg: state.difficulty==='difficile'?[3,5]:[2,4], frozenNext:false, burnNext:0 };
  state.enemy = bot;
  renderCombatUI(true);
  log('🤖 Épreuve Combat : affronte le Robot d\'entraînement (3 tours).');
}

/* ===== Training: Rescue (choix de porte) ===== */
function startRescue(){
  // player chooses 1 of 3 portes; only one porte a l'otage
  const correct = rand(1,3);
  const attempts = 1;
  state.temp = { correctDoor: correct };
  $('challengeBox').innerHTML = `<div class="small">🚨 Sauvetage : un otage simulé est caché derrière 1 porte. Choisis la bonne porte (1-3).</div>`;
  const actions = $('actionButtons'); actions.innerHTML='';
  for(let i=1;i<=3;i++){
    const b=document.createElement('button'); b.textContent='Porte '+i;
    b.onclick = ()=>{ resolveRescue(i); };
    actions.appendChild(b);
  }
  log('🚪 Épreuve Sauvetage : choisit la porte correcte.');
}

/* ===== Training: Puzzle (QCM) ===== */
const QUIZZES = {
  easy: [
    {q:'Quelle tactique permet d\'éviter une attaque frontale ?',
     choices:['Attaque frontale','Esquive latérale','Ignorer l\'ennemi'], a:1}
  ],
  normal: [
    {q:'Quelle option est la plus efficace pour neutraliser une menace multiple ?', choices:['Fuite','Zone d\'effet','Attaque ciblée'], a:1},
    {q:'Lors d’une embuscade, que fais-tu en priorité ?', choices:['Appeler renforts','Analyser et esquiver','Se reposer'], a:1}
  ],
  hard: [
    {q:'Comment réduire l’efficacité d’un ennemi blindé ?', choices:['Attaquer la source d’énergie','Donner des coups aléatoires','Provoquer distraction'], a:0},
    {q:'Quelle action pour éviter d’être ciblé par un tir automatique ?', choices:['Rester immobile','Se couvrir puis se déplacer','Siffler'], a:1}
  ]
};
function startPuzzle(){
  // pick a quiz according to difficulty
  let pool = state.difficulty==='facile'?QUIZZES.easy:(state.difficulty==='difficile'?QUIZZES.hard:QUIZZES.normal);
  const q = sample(pool);
  state.temp = { quiz:q };
  renderQuiz(q);
  log('🧠 Épreuve Stratégie : résous le QCM.');
}
function renderQuiz(q){
  $('challengeBox').innerHTML = `<div class="small"><b>Question :</b> ${q.q}</div>`;
  const actions = $('actionButtons'); actions.innerHTML='';
  q.choices.forEach((c,i)=>{
    const b=document.createElement('button'); b.textContent=c; b.onclick=(()=>{ resolveQuiz(i); }); actions.appendChild(b);
  });
}

/* ===== Resolvers for Rescue & Puzzle ===== */
function resolveRescue(choice){
  const correct = state.temp.correctDoor;
  $('actionButtons').innerHTML='';
  if(choice===correct){
    log('🟢 Sauvetage réussi ! Tu retrouves l\'otage.');
    spawnFloating($('playerEmoji'),'+10','lightgreen');
    state.score += 10; setTimeout(()=>{ state.stage++; nextChallenge(); },600);
  } else {
    log('🔴 Porte vide ! Piège simulé, légère blessure.');
    spawnFloating($('playerEmoji'),'-6','red');
    state.player.pv -= 6; if(state.player.pv<0) state.player.pv=0;
    state.score -= 2;
    updatePlayerUI();
    setTimeout(()=>{ state.stage++; nextChallenge(); },700);
  }
}

function resolveQuiz(choiceIdx){
  const q = state.temp.quiz;
  const correct = q.a;
  $('actionButtons').innerHTML='';
  if(choiceIdx===correct){
    log('🟢 Bonne réponse — tu gagnes en tactique.');
    state.score += 6;
  } else {
    log('🔴 Mauvaise réponse — tu perds un temps précieux.');
    state.player.pv -= 4; if(state.player.pv<0) state.player.pv=0;
    state.score -= 1;
    updatePlayerUI();
  }
  state.stage++; setTimeout(nextChallenge,600);
}

/* ===== Combat UI render (used for both training bot & final) ===== */
function renderCombatUI(isTraining){
  const box = $('challengeBox'); box.innerHTML = `<div class="small"><b>${state.enemy.emoji} ${state.enemy.name}</b><div class="small" id="enemyPVtxt">PV: ${state.enemy.pv} / ${state.enemy.max}</div><div class="hpbar" style="margin-top:6px"><div id="enemyHP" class="fill enemy" style="width:${(state.enemy.pv/state.enemy.max*100)}%"></div></div></div>`;
  const actions = $('actionButtons'); actions.innerHTML='';

  // actions: light, heavy, use power(if available), defend
  const a1 = document.createElement('button'); a1.textContent='⚔️ Coup léger (2-4)'; a1.onclick=()=>playerAttack('light');
  const a2 = document.createElement('button'); a2.textContent='💥 Coup fort (4-7)'; a2.onclick=()=>playerAttack('heavy');
  const a3 = document.createElement('button'); a3.textContent='🛡️ Défendre (réduit dégâts)'; a3.onclick=()=>playerDefend();
  actions.appendChild(a1); actions.appendChild(a2); actions.appendChild(a3);

  if(state.player.hasPower){
    const a4 = document.createElement('button'); a4.textContent=`✨ Utiliser ${state.chosenPower.name}`; a4.onclick=()=>usePower();
    actions.appendChild(a4);
  }

  // show count if training (3 turns) else final combat display
  if(isTraining){
    box.insertAdjacentHTML('beforeend', '<div class="note">Objectif : vaincre le robot (3 tours max).</div>');
  } else {
    box.insertAdjacentHTML('beforeend', '<div class="note">Combat final — échange tour par tour jusqu\'à la victoire.</div>');
  }
}

/* ===== Player combat actions ===== */
function playerAttack(kind){
  // determine dmg
  let dmg = 0;
  if(kind==='light') dmg = rand(2,4);
  else dmg = rand(4,7);
  // claws power: if queued, multiply
  if(state.player.powerNext === 'claws'){
    dmg += 6; state.player.powerNext = null; log('🗡️ Griffes : attaque renforcée ! (+6)');
  }
  // apply to enemy
  state.enemy.pv -= dmg; if(state.enemy.pv<0) state.enemy.pv=0;
  log(`🦸 Tu infliges ${dmg} PV à ${state.enemy.name}`);
  spawnFloating($('enemyHP'),`-${dmg}`,'#ffcccb');
  updateEnemyUI();
  // scoring: if damage to training bot, small points
  if(state.enemy.name.includes('Robot')) state.score += 2;
  // enemy reaction unless frozen
  setTimeout(()=>enemyReact(),450);
}

function playerDefend(){
  state.player.evadeBuff = Math.min(0.5, state.player.evadeBuff + 0.25); // stack small evade
  log('🛡️ Tu te mets en garde (réduction de dégâts next).');
  // enemy reacts
  setTimeout(()=>enemyReact(),350);
}

function usePower(){
  if(!state.player.hasPower) return;
  const p = state.chosenPower;
  // apply power effect (use effect function)
  p.effect({ player: state.player, enemy: state.enemy, log, spawnFloating, state });
  updatePlayerUI();
  // if power modifies enemy properties (burn/frozen) those must be on enemy object
  // if effect set extraAction, allow immediate extra action
  if(state.player.extraAction){
    // allow one immediate extra action: leave action buttons but won't auto enemy react until actions used.
    log('🔁 Tu as une action supplémentaire disponible (utilise-la maintenant).');
    // do not call enemyReact here; the extraAction flag will be consumed on next attack.
  } else {
    // normal: enemy reacts
    setTimeout(()=>enemyReact(),500);
  }
}

/* ===== Enemy reaction (simple AI) ===== */
function enemyReact(){
  if(!state.enemy) return;
  // if frozen, skip attack and unfreeze
  if(state.enemy.frozenNext){
    log(`❄️ ${state.enemy.name} est gelé et manque son action !`);
    state.enemy.frozenNext = false;
    // reduce burn if any
    if(state.enemy.burnNext) { state.enemy.burnNext--; const bd = 2; state.enemy.pv -= bd; log(`🔥 Brûlure : ${state.enemy.name} subit ${bd} dégâts.`); spawnFloating($('enemyHP'),`-${bd}`,'#ffb86b'); updateEnemyUI(); }
    checkCombatEnd();
    return;
  }

  // enemy deals damage (unless death)
  if(state.enemy.pv <= 0){ checkCombatEnd(); return; }

  // simple damage
  let dmg = rand(state.enemy.dmg[0], state.enemy.dmg[1]);
  // apply player's evadeBuff (percentage)
  const evade = state.player.evadeBuff || 0;
  const reduced = Math.round(dmg * evade);
  dmg = Math.max(0, dmg - reduced);
  state.player.pv -= dmg; if(state.player.pv<0) state.player.pv=0;
  log(`💥 ${state.enemy.name} riposte : ${dmg} PV infligés.`);
  spawnFloating($('playerHP'),`-${dmg}`,'#ff8897');
  // reset player's small evade buff (consumed)
  state.player.evadeBuff = 0;
  // apply enemy burn or other effects
  if(state.enemy.burnNext && state.enemy.burnNext>0){
    // enemy burning? Actually burn applied to enemy, handled at top of their turn
  }
  updatePlayerUI();
  checkCombatEnd();
}

/* ===== Update enemy UI ===== */
function updateEnemyUI(){
  $('enemyPVtxt')?.textContent = `PV: ${state.enemy.pv} / ${state.enemy.max}`;
  const el = $('enemyHP'); if(el) el.style.width = Math.max(0,(state.enemy.pv/state.enemy.max*100))+'%';
}

/* ===== Check combat end (training robot or final) ===== */
function checkCombatEnd(){
  if(state.enemy && state.enemy.pv <= 0){
    log(`🏁 ${state.enemy.name} neutralisé !`);
    // reward based on training or final
    if(state.stage < 3){
      // training combat success
      state.score += 8;
      state.stage++;
      setTimeout(()=> nextChallenge(),700);
    } else {
      // final: win
      state.score += 20;
      concludeFinal(true);
    }
  } else if(state.player.pv <= 0){
    log(`💀 Tu es inconscient... échec.`);
    if(state.stage < 3){
      // training fail
      setTimeout(()=> concludeTrainingFail(),600);
    } else {
      concludeFinal(false);
    }
  } else {
    // if training combat and there are limited turns, allow retry until turn count; we keep simple: let fight continue until one dies
  }
}

/* ===== Training conclude fail ===== */
function concludeTrainingFail(){
  endGame('❌ Tu as échoué dans l’entraînement', 'Tes PV sont tombés à 0. Recommence l’entraînement pour t’améliorer.');
}

/* ===== Start final boss preparaton ===== */
function prepareFinal(){
  state.stage = 3;
  $('stageTxt').textContent = '— Combat final';
  // pick villain randomly
  const v = sample(VILLAINS);
  state.enemy = { name:v.name, emoji:v.emoji, pv: Math.round(v.pvBase * (state.difficulty==='difficile'?1.15:(state.difficulty==='facile'?0.9:1))), max: Math.round(v.pvBase * (state.difficulty==='difficile'?1.15:(state.difficulty==='facile'?0.9:1))), dmg: v.dmg, frozenNext:false, burnNext:0 };
  log(`⚠️ Final : Simulateur ${state.enemy.name} activé. Prépare-toi.`);
  renderCombatUI(false);
}

/* ===== Final conclude ===== */
function concludeFinal(victory){
  if(victory){
    // evaluate score-based endings
    let title = '🏆 Succès — Tu es admis à l\'Académie !';
    let text;
    if(state.score >= 45) text = '🎖️ <b>Admis avec mention</b> — Tu es un atout pour l’équipe.';
    else if(state.score >= 25) text = '✅ Admis — Bon potentiel, continue l’entraînement.';
    else text = '⚠️ Admis en formation — Tu dois encore travailler tes compétences.';
    endGame(title, `${text}<br><br>Score final : <b>${state.score}</b>`);
  } else {
    endGame('💥 Défaite', `Le simulateur t'as surpassé. Score final : <b>${state.score}</b>. Recommence l'entraînement pour améliorer ton résultat.`);
  }
}

/* ===== Helpers & event tweaks ===== */
function updatePlayerUI(){ $('playerPVtxt').textContent = `PV: ${state.player.pv} / ${state.player.max}`; $('playerHP').style.width = Math.max(0,(state.player.pv/state.player.max*100))+'%'; $('playerEmoji').textContent = state.player.emoji; $('score').textContent = state.score; }

</script>
</body>
</html>
