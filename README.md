<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Migrant Boat Game</title>
  <style>
    body {
      margin: 0;
      overflow: hidden;
      background: linear-gradient(to top, #1e3c72, #2a5298);
      font-family: Arial, sans-serif;
      color: white;
      text-align: center;
    }
    canvas {
      display: block;
      margin: 0 auto;
      background: url('https://i.ibb.co/6vM6Kd0/ocean.png') repeat-x;
    }
    #info {
      position: absolute;
      top: 10px;
      width: 100%;
      font-size: 18px;
    }
    a {
      color: yellow;
      text-decoration: none;
    }
  </style>
</head>
<body>
  <div id="info">
    Score: <span id="score">0</span> | 
    <a href="https://www.youtube.com/@CrazyMandem" target="_blank">Visit my YouTube</a>
  </div>
  <canvas id="gameCanvas" width="600" height="400"></canvas>

  <script>
    const canvas = document.getElementById('gameCanvas');
    const ctx = canvas.getContext('2d');

    // Boat player
    const boat = {
      x: 50,
      y: canvas.height / 2 - 25,
      width: 50,
      height: 30,
      speed: 5
    };

    // Obstacles (waves and coast patrol)
    const obstacles = [];
    let frameCount = 0;
    let score = 0;
    const scoreEl = document.getElementById('score');

    // Controls
    const keys = {};
    window.addEventListener('keydown', e => keys[e.key] = true);
    window.addEventListener('keyup', e => keys[e.key] = false);

    function spawnObstacle() {
      const height = Math.random() * 50 + 20;
      const y = Math.random() * (canvas.height - height);
      obstacles.push({x: canvas.width, y: y, width: 30, height: height});
    }

    function update() {
      // Move boat
      if (keys['ArrowUp'] && boat.y > 0) boat.y -= boat.speed;
      if (keys['ArrowDown'] && boat.y + boat.height < canvas.height) boat.y += boat.speed;

      // Spawn obstacles
      frameCount++;
      if (frameCount % 90 === 0) spawnObstacle();

      // Move obstacles
      for (let i = obstacles.length - 1; i >= 0; i--) {
        obstacles[i].x -= 4;
        if (obstacles[i].x + obstacles[i].width < 0) {
          obstacles.splice(i, 1);
          score++;
          scoreEl.textContent = score;
        }
      }

      // Check collision
      for (let obs of obstacles) {
        if (boat.x < obs.x + obs.width &&
            boat.x + boat.width > obs.x &&
            boat.y < obs.y + obs.height &&
            boat.y + boat.height > obs.y) {
          alert('Game Over! You tried to reach the UK.');
          score = 0;
          scoreEl.textContent = score;
          boat.y = canvas.height / 2 - 25;
          obstacles.length = 0;
        }
      }
    }

    function draw() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);

      // Draw boat
      ctx.fillStyle = '#FFD700';
      ctx.fillRect(boat.x, boat.y, boat.width, boat.height);

      // Draw obstacles
      ctx.fillStyle = '#FF4500';
      for (let obs of obstacles) {
        ctx.fillRect(obs.x, obs.y, obs.width, obs.height);
      }
    }

    function gameLoop() {
      update();
      draw();
      requestAnimationFrame(gameLoop);
    }

    gameLoop();
  </script>
</body>
</html>
