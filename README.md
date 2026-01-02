<!DOCTYPE html>
<html lang="hr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Kliker Run</title>

<style>
body {
  margin: 0;
  background: #0f172a;
  color: white;
  font-family: Arial;
  display: flex;
  flex-direction: column;
  align-items: center;
}

canvas {
  background: #020617;
  margin-top: 10px;
  border-radius: 12px;
}

.controls {
  display: flex;
  gap: 30px;
  margin: 15px;
}

.btn {
  width: 70px;
  height: 70px;
  font-size: 30px;
  border-radius: 50%;
  border: none;
  background: #2563eb;
  color: white;
}

.blink {
  animation: blink 1s infinite;
}

@keyframes blink {
  0% { opacity: 1; }
  50% { opacity: 0; }
  100% { opacity: 1; }
}
</style>
</head>

<body>

<h2>KLIKER RUN</h2>
<canvas id="game" width="360" height="520"></canvas>

<div class="controls">
  <button class="btn" id="left">⬅️</button>
  <button class="btn" id="right">➡️</button>
</div>

<!-- GAME OVER ZVUK -->
<audio id="gameOverSound">
  <source src="https://actions.google.com/sounds/v1/cartoon/clang_and_wobble.ogg" type="audio/ogg">
</audio>

<script>
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");
const sound = document.getElementById("gameOverSound");

let ball = { x: 180, y: 460, r: 12 };
let speed = 6;
let obstacles = [];
let score = 0;
let gameOver = false;
let soundPlayed = false;

function spawnObstacle() {
  obstacles.push({
    x: Math.random() * 320,
    y: -20,
    w: 40,
    h: 20
  });
}

function drawBall() {
  ctx.beginPath();
  ctx.arc(ball.x, ball.y, ball.r, 0, Math.PI * 2);
  ctx.fillStyle = "#22d3ee";
  ctx.fill();
}

function drawObstacles() {
  ctx.fillStyle = "#ef4444";
  obstacles.forEach(o => ctx.fillRect(o.x, o.y, o.w, o.h));
}

function update() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  if (!gameOver) {
    if (Math.random() < 0.03) spawnObstacle();

    obstacles.forEach(o => {
      o.y += 4;

      if (
        ball.x + ball.r > o.x &&
        ball.x - ball.r < o.x + o.w &&
        ball.y + ball.r > o.y &&
        ball.y - ball.r < o.y + o.h
      ) {
        gameOver = true;
      }

      if (o.y > 520) score++;
    });

    obstacles = obstacles.filter(o => o.y < 600);
  }

  drawBall();
  drawObstacles();

  ctx.fillStyle = "white";
  ctx.font = "18px Arial";
  ctx.fillText("Score: " + score, 10, 25);

  if (gameOver) {
    if (!soundPlayed) {
      sound.play();
      soundPlayed = true;
    }

    ctx.font = "22px Arial";
    ctx.fillStyle = "red";
    ctx.fillText("GAME OVER", 105, 250);

    ctx.font = "16px Arial";
    ctx.fillStyle = "white";
    ctx.fillText("CLICK TO RESTART", 95, 280);
  }

  requestAnimationFrame(update);
}

document.getElementById("left").onclick = () => {
  if (!gameOver) ball.x -= speed;
};
document.getElementById("right").onclick = () => {
  if (!gameOver) ball.x += speed;
};

document.addEventListener("keydown", e => {
  if (e.key === "ArrowLeft") ball.x -= speed;
  if (e.key === "ArrowRight") ball.x += speed;
});

document.addEventListener("click", () => {
  if (gameOver) location.reload();
});

update();
</script>

</body>
</html>
