# d1ord2<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Shooter Game</title>
    <style>
        body {
            margin: 0;
            overflow: hidden;
            font-family: Arial, sans-serif;
        }
        canvas {
            display: block;
        }
    </style>
</head>
<body>
    <canvas id="gameCanvas"></canvas>
    <script>
        // Set up canvas
        const canvas = document.getElementById("gameCanvas");
        const ctx = canvas.getContext("2d");
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;

        // Player setup
        const player = {
            x: canvas.width / 2,
            y: canvas.height - 50,
            width: 30,
            height: 30,
            color: "blue",
            speed: 5,
            bullets: []
        };

        // Enemy setup
        const enemies = [];
        const enemySize = 30;
        const enemySpeed = 2;

        // Game controls
        const keys = {
            ArrowLeft: false,
            ArrowRight: false,
            Space: false
        };

        // Event listeners for controls
        window.addEventListener("keydown", (e) => {
            if (keys.hasOwnProperty(e.code)) keys[e.code] = true;
        });

        window.addEventListener("keyup", (e) => {
            if (keys.hasOwnProperty(e.code)) keys[e.code] = false;
        });

        // Spawn enemies
        function spawnEnemy() {
            const x = Math.random() * (canvas.width - enemySize);
            enemies.push({ x, y: -enemySize, width: enemySize, height: enemySize, color: "red" });
        }

        setInterval(spawnEnemy, 1000);

        // Update game state
        function update() {
            // Move player
            if (keys.ArrowLeft && player.x > 0) player.x -= player.speed;
            if (keys.ArrowRight && player.x + player.width < canvas.width) player.x += player.speed;

            // Fire bullets
            if (keys.Space) {
                player.bullets.push({ x: player.x + player.width / 2 - 2.5, y: player.y, width: 5, height: 10, color: "yellow" });
                keys.Space = false; // Prevent continuous firing
            }

            // Move bullets
            player.bullets.forEach((bullet, index) => {
                bullet.y -= 7;
                if (bullet.y + bullet.height < 0) player.bullets.splice(index, 1);
            });

            // Move enemies
            enemies.forEach((enemy, index) => {
                enemy.y += enemySpeed;
                if (enemy.y > canvas.height) enemies.splice(index, 1);
            });

            // Check collisions
            player.bullets.forEach((bullet, bIndex) => {
                enemies.forEach((enemy, eIndex) => {
                    if (
                        bullet.x < enemy.x + enemy.width &&
                        bullet.x + bullet.width > enemy.x &&
                        bullet.y < enemy.y + enemy.height &&
                        bullet.y + bullet.height > enemy.y
                    ) {
                        // Remove bullet and enemy on collision
                        player.bullets.splice(bIndex, 1);
                        enemies.splice(eIndex, 1);
                    }
                });
            });
        }

        // Draw everything
        function draw() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Draw player
            ctx.fillStyle = player.color;
            ctx.fillRect(player.x, player.y, player.width, player.height);

            // Draw bullets
            player.bullets.forEach((bullet) => {
                ctx.fillStyle = bullet.color;
                ctx.fillRect(bullet.x, bullet.y, bullet.width, bullet.height);
            });

            // Draw enemies
            enemies.forEach((enemy) => {
                ctx.fillStyle = enemy.color;
                ctx.fillRect(enemy.x, enemy.y, enemy.width, enemy.height);
            });
        }

        // Main game loop
        function gameLoop() {
            update();
            draw();
            requestAnimationFrame(gameLoop);
        }

        gameLoop();
    </script>
</body>
</html>
