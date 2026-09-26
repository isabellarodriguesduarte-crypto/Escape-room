# Escape-room
<!DOCTYPE html>
<html lang="pt">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Escape Lab: CRISPR</title>
<style>
:root {
--bg: #030712;
--panel: #0f172a;
--accent: #06b6d4;
--accent-glow: rgba(6, 182, 212, 0.3);
--danger: #ef4444;
--success: #10b981;
--text: #f8fafc;
}
body {
font-family: 'Segoe UI', system-ui, sans-serif;
background: radial-gradient(circle at center, #1e293b, var(--bg));
color: var(--text);
margin: 0;
padding: 15px;
display: flex;
justify-content: center;
align-items: center;
min-height: 100vh;
}
.escape-container {
width: 100%;
max-width: 600px;
background: var(--panel);
border: 2px solid rgba(6, 182, 212, 0.4);
border-radius: 20px;
padding: 25px;
box-shadow: 0 0 50px rgba(0, 0, 0, 0.9), inset 0 0 20px var(--accent-glow);
box-sizing: border-box;
}
h1, h2 {
color: var(--accent);
text-align: center;
margin-top: 0;
}
.screen { display: none; } 

.form-group {
margin-bottom: 20px;
}
label { display: block; margin-bottom: 8px; font-size: 0.9rem; color: #cbd5e1; }
input[type="text"] {
width: 100%;
padding: 12px;
background: rgba(255, 255, 255, 0.05);
border: 1px solid rgba(255, 255, 255, 0.2);
border-radius: 10px;
color: white;
font-size: 1rem;
box-sizing: border-box;
}
input[type="text"]:focus {
border-color: var(--accent);
outline: none;
} 

.btn {
background: var(--accent);
color: #000;
border: none;
padding: 14px;
width: 100%;
border-radius: 10px;
font-weight: bold;
font-size: 1rem;
cursor: pointer;
transition: all 0.2s;
}
.btn:hover {
opacity: 0.9;
transform: translateY(-2px);
} 

.hud {
display: flex;
justify-content: space-between;
background: rgba(0,0,0,0.4);
padding: 10px 15px;
border-radius: 8px;
margin-bottom: 20px;
font-size: 0.9rem;
font-weight: bold;
}
.room-box {
background: rgba(255, 255, 255, 0.03);
border: 1px solid rgba(255, 255, 255, 0.1);
padding: 15px;
border-radius: 12px;
margin-bottom: 20px;
line-height: 1.5;
}
.clue-grid {
display: grid;
grid-template-columns: 1fr;
gap: 10px;
}
.clue-btn {
background: rgba(255, 255, 255, 0.05);
border: 1px solid rgba(255, 255, 255, 0.15);
color: white;
padding: 14px 15px;
border-radius: 10px;
cursor: pointer;
text-align: left;
transition: all 0.2s;
font-size: 0.95rem;
font-family: inherit;
display: flex;
align-items: center;
gap: 10px;
}
.clue-btn:hover {
background: rgba(6, 182, 212, 0.15);
border-color: var(--accent);
transform: translateX(4px);
} 

.ranking-table {
width: 100%;
border-collapse: collapse;
margin-top: 15px;
margin-bottom: 20px;
}
.ranking-table th, .ranking-table td {
padding: 10px;
text-align: left;
border-bottom: 1px solid rgba(255, 255, 255, 0.1);
font-size: 0.9rem;
}
.ranking-table th { color: var(--accent); } 

#feedback {
margin-top: 15px;
padding: 10px;
border-radius: 8px;
text-align: center;
font-weight: bold;
font-size: 0.9rem;
}
</style>
</head>
<body> 

<div class="escape-container"> 

<!-- ECRÃ DE REGISTO -->
<div id="screen-welcome" class="screen" style="display: block;">
<h1>🔓 Escape Lab: CRISPR</h1>
<p style="text-align: center; color: #94a3b8; font-size: 0.95rem; margin-bottom: 25px;">
Estás trancado no núcleo infetado. Resolve os 3 desafios de edição genética o mais rápido possível para escapar e entrar no pódio!
</p>
<div class="form-group">
<label for="player-name">Nome do Participante / Equipa:</label>
<input type="text" id="player-name" placeholder="Ex: Ana e João" autocomplete="off">
</div>
<!-- Alterado para chamar a função diretamente via onclick -->
<button class="btn" id="start-btn" type="button" onclick="startGame()">INICIAR FUGA 🚀</button> 

<div style="margin-top: 25px;">
<h3 style="font-size: 1rem; margin-bottom: 5px;">🏆 Top da Banca</h3>
<table class="ranking-table" id="preview-ranking">
<tr><th>Pos</th><th>Nome</th><th>Tempo</th></tr>
<tr><td colspan="3" style="text-align: center; color: #64748b;">Ainda sem registos</td></tr>
</table>
</div>
</div> 

<!-- ECRÃ DO JOGO -->
<div id="screen-game" class="screen" style="display: none;">
<div class="hud">
<span id="room-title">Desafio 1/3</span>
<span id="timer" style="color: var(--accent);">⏱️ 0s</span>
</div> 

<div class="room-box">
<div id="room-desc" style="font-size: 0.95rem;">A carregar pista...</div>
</div> 

<div class="clue-grid" id="options-box"></div>
<div id="feedback"></div> 

<!-- BOTÃO VOLTAR AO INÍCIO -->
<button class="btn" type="button" onclick="resetToWelcome()" style="background: transparent; border: 1px solid var(--accent); color: var(--accent); margin-top: 20px; padding: 10px;">❮ VOLTAR AO INÍCIO</button>
</div> 

<!-- ECRÃ DE VITÓRIA -->
<div id="screen-victory" class="screen" style="display: none;">
<h1 style="color: var(--success);">🎉 Célula Libertada!</h1>
<p id="final-stats" style="text-align: center; font-size: 1.1rem; margin-bottom: 20px;"></p> 

<h3 style="margin-bottom: 5px;">🏆 Tabela de Classificação Oficial</h3>
<table class="ranking-table" id="final-ranking">
<tr><th>Pos</th><th>Nome</th><th>Tempo</th></tr>
</table> 

<button class="btn" id="restart-btn" type="button" onclick="resetToWelcome()" style="margin-top: 15px;">PRÓXIMO PARTICIPANTE 🔄</button>
</div> 

</div> 

<script>
let playerName = "";
let startTime = 0;
let timerInterval = null;
let currentRoom = 1;
let elapsedTime = 0; 

const rooms = [
{
title: "Desafio 1: O GPS Celular",
desc: "<strong>Situação:</strong> O núcleo está infetado e precisamos de encontrar o endereço exato da mutação no ADN. Qual componente atua como o localizador?",
correct: 1,
options: [
"Usar uma tesoura comum sem direção",
"Colocar o guia molecular para encontrar o endereço exato",
"Destruir toda a membrana da célula"
]
},
{
title: "Desafio 2: O Corte Cirúrgico",
desc: "<strong>Situação:</strong> O endereço foi encontrado com sucesso! Que ferramenta deve ser acionada agora para retirar a parte estragada do ADN?",
correct: 0,
options: [
"Acionar a tesoura molecular para fazer o corte limpo",
"Aquecer o núcleo inteiro até derreter",
"Deixar o ADN fechado e ignorar o erro"
]
},
{
title: "Desafio 3: A Reescrita Final",
desc: "<strong>Situação:</strong> O corte foi limpo. Para fechar a fita de ADN e curar a célula em definitivo, o que devemos utilizar?",
correct: 2,
options: [
"Espalhar um vírus ativo para destruir o espaço",
"Deixar o local aberto sem reparo",
"Utilizar um molde de correção saudável para fechar a fita"
]
}
]; 

function mudarTela(telaAtivaId) {
document.getElementById("screen-welcome").style.display = "none";
document.getElementById("screen-game").style.display = "none";
document.getElementById("screen-victory").style.display = "none"; 

document.getElementById(telaAtivaId).style.display = "block";
} 

function startGame() {
const input = document.getElementById("player-name");
playerName = input.value.trim(); 

if(!playerName) {
alert("Por favor, insere o nome do participante ou da equipa!");
return;
} 

currentRoom = 1;
elapsedTime = 0; 

mudarTela("screen-game"); 

startTime = Date.now();
if(timerInterval) clearInterval(timerInterval); 

timerInterval = setInterval(() => {
elapsedTime = Math.floor((Date.now() - startTime) / 1000);
const timerEl = document.getElementById("timer");
if(timerEl) timerEl.innerText = `⏱️ ${elapsedTime}s`;
}, 1000); 

loadRoom();
} 

function loadRoom() {
const room = rooms[currentRoom - 1];
document.getElementById("room-title").innerText = `Desafio ${currentRoom}/3`;
document.getElementById("room-desc").innerHTML = room.desc;
document.getElementById("feedback").innerHTML = ""; 

const box = document.getElementById("options-box");
box.innerHTML = ""; 

room.options.forEach((opt, index) => {
const btn = document.createElement("button");
btn.className = "clue-btn";
btn.type = "button";
btn.innerHTML = `➔ ${opt}`;
btn.onclick = () => checkAnswer(index, room.correct);
box.appendChild(btn);
});
} 

function checkAnswer(selectedIndex, correctIndex) {
const feedback = document.getElementById("feedback");
if(selectedIndex === correctIndex) {
feedback.style.background = "rgba(16, 185, 129, 0.2)";
feedback.style.color = "var(--success)";
feedback.innerHTML = "✅ Correto! A avançar para a próxima etapa..."; 

setTimeout(() => {
currentRoom++;
if(currentRoom > rooms.length) {
endGame();
} else {
loadRoom();
}
}, 1200);
} else {
feedback.style.background = "rgba(239, 68, 68, 0.2)";
feedback.style.color = "var(--danger)";
feedback.innerHTML = "❌ Erro no sistema! Tenta outra opção.";
}
} 

function endGame() {
clearInterval(timerInterval);
mudarTela("screen-victory"); 

document.getElementById("final-stats").innerHTML = `Parabéns, <strong>${playerName}</strong>!<br>Conseguiste escapar do laboratório em <strong>${elapsedTime} segundos</strong>!`; 

saveScore(playerName, elapsedTime);
renderRankings();
} 

function saveScore(name, time) {
try {
let scores = JSON.parse(localStorage.getItem("crispr_ranking") || "[]");
scores.push({ name, time });
scores.sort((a, b) => a.time - b.time);
localStorage.setItem("crispr_ranking", JSON.stringify(scores));
} catch(e) {
// Fallback caso o navegador bloqueie o localStorage em arquivos locais
console.log("Armazenamento local indisponível");
}
} 

function renderRankings() {
try {
let scores = JSON.parse(localStorage.getItem("crispr_ranking") || "[]");
let html = `<tr><th>Pos</th><th>Nome</th><th>Tempo</th></tr>`;
if(scores.length === 0) {
html += `<tr><td colspan="3" style="text-align: center; color: #64748b;">Ainda sem registos</td></tr>`;
} else {
scores.slice(0, 5).forEach((s, idx) => {
html += `<tr><td>#${idx+1}</td><td>${s.name}</td><td>${s.time}s</td></tr>`;
});
}
const finalRanking = document.getElementById("final-ranking");
const previewRanking = document.getElementById("preview-ranking");
if(finalRanking) finalRanking.innerHTML = html;
if(previewRanking) previewRanking.innerHTML = html;
} catch(e) {
// Ignora erros de armazenamento local restrito
}
} 

function resetToWelcome() {
if(timerInterval) clearInterval(timerInterval);
mudarTela("screen-welcome");
const inputName = document.getElementById("player-name");
if(inputName) inputName.value = "";
renderRankings();
} 

// Inicializa o ranking assim que o script carregar
renderRankings();
</script> 

</body>
</html>
