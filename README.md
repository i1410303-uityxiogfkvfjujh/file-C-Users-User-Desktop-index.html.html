<html lang="zh-TW">
<head>
<meta charset="UTF-8">
<title>射擊遊戲 V3（Boss 修正版）</title>

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
  z-index: 10;
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
  z-index: 20;
}

button {
  margin-top: 20px;
  padding: 10px 20px;
  font-size: 20px;
}
</style>
</head>

<body>

<div id="ui">
  分數：<span id="score">0</span><br>
  生命：<span id="h

