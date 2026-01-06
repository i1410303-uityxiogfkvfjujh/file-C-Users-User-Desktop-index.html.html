<!DOCTYPE html>
<html lang="zh-TW">
<head>
<meta charset="UTF-8">
<title>射擊遊戲 V4（結構優化版）</title>
<style>
body {
  margin: 0;
  overflow: hidden;
  cursor: none;
  background: #111;
  color: white;
  font-family: Arial;
}
#ui {
  position: fixed;
  top: 10px;
  left: 10px;
  font-size: 20px;
}
.enemy, .boss {
  position: absolute;
  border-radius: 6px;
}
.enemy {
  width: 40px;
  height: 40px;
}
.boss {
  width: 100px;
  height: 100px;
  background: darkorange;
  border: 4px solid white;
}
#crosshair {
  position: absolute;
  width: 30px;
  height: 30px;
  border: 2px solid white;
  border-radius: 50%;
  pointer-events: none;
  transform: translate(-50%, -50%);
}
#gameover {
  position: fixed;
  inset: 0;
  background: rgba(0,0,0,0.85);
  display: none;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  font-size: 32px;
}
</style>
</head>
<body>

<div id="ui">
分數：<span id="score">0</span><br>
生命：<span id="hp">3</span>
</div>

<div id="crosshair"></div>

<div id="gameover">
  <div>遊戲結束</div>
  <div id="finalScore"></div>
  <button onclick="location.reload()">重新開始</button>
</div>

<script>
/* ========= 基本狀態 ========= */
const ui = {
  score: document.getElementById("score"),
  hp: document.getElementById("hp"),
  gameover: document.getElementById("gameover"),
  finalScore: document.getElementById("finalScore"),
  crosshair: document.getElementById("crosshair")
};

const game = {
  score: 0,
  hp: 3,
  running: true,
  lastBossScore: 0
};

const enemies = [];
let boss = null;

let mouse = { x: innerWidth / 2, y: innerHeight / 2 };

/* ========= 工具 ========= */
function hitTest(obj) {
  return (
    mouse.x > obj.x &&
    mouse.x < obj.x + obj.size &&
    mouse.y > obj.y &&
    mouse.y < obj.y + obj.size
  );
}

/* ========= 敵人 ========= */
function spawnEnemy() {
  if (boss) return;

  const el = document.createElement("div");
  el.className = "enemy";

  const r = Math.random();
  let hp = 1, point = 1, speed = 2, color = "red";

  if (r > 0.6) { color = "dodgerblue"; point = 2; speed = 4; }
  if (r > 0.85) { color = "purple"; hp = 2; point = 3; speed = 1.5; }

  el.style.background = color;
  document.body.appendChild(el);

  enemies.push({
    el,
    x: Math.random() * (innerWidth - 40),
    y: Math.random() * (innerHeight - 40),
    dx: (Math.random() - 0.5) * speed,
    dy: (Math.random() - 0.5) * speed,
    size: 40,
    hp,
    point,
    life: 3500
  });
}

/* ========= Boss ========= */
function spawnBoss() {
  const el = document.createElement("div");
  el.className = "boss";
  document.body.appendChild(el);

  boss = {
    el,
    x: innerWidth / 2 - 50,
    y: 50,
    size: 100,
    hp: 10
  };
}

/* ========= 輸入 ========= */
document.addEventListener("mousemove", e => {
  mouse.x = e.clientX;
  mouse.y = e.clientY;
  ui.crosshair.style.left = mouse.x + "px";
  ui.crosshair.style.top = mouse.y + "px";
});

document.addEventListener("mousedown", () => {
  if (!game.running) return;

  for (let i = enemies.length - 1; i >= 0; i--) {
    if (hitTest(enemies[i])) {
      enemies[i].hp--;
      if (enemies[i].hp <= 0) {
        game.score += enemies[i].point;
        enemies[i].el.remove();
        enemies.splice(i, 1);
      }
      return;
    }
  }

  if (boss && hitTest(boss)) {
    boss.hp--;
    if (boss.hp <= 0) {
      boss.el.remove();
      boss = null;
      game.score += 10;
      game.hp++;
      game.lastBossScore = game.score;
    }
  }
});

/* ========= 更新 ========= */
function update(dt) {
  enemies.forEach((e, i) => {
    e.x += e.dx;
    e.y += e.dy;
    e.life -= dt;

    if (e.x < 0 || e.x > innerWidth - e.size) e.dx *= -1;
    if (e.y < 0 || e.y > innerHeight - e.size) e.dy *= -1;

    if (e.life <= 0) {
      e.el.remove();
      enemies.splice(i, 1);
      game.hp--;
      if (game.hp <= 0) endGame();
    }
  });

  if (boss) {
    boss.x += (mouse.x - boss.x - 50) * 0.02;
    boss.y += (mouse.y - boss.y - 50) * 0.02;
    boss.x = Math.max(0, Math.min(innerWidth - 100, boss.x));
    boss.y = Math.max(0, Math.min(innerHeight - 100, boss.y));
  }
}

/
