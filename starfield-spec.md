# Swift Starfield - Pokemon Inspired

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Swift Starfield</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            background: #0a0a1a;
            overflow: hidden;
            font-family: 'Arial', sans-serif;
        }

        canvas {
            display: block;
            background: radial-gradient(circle at center, #1a1a3a 0%, #050510 100%);
        }

        .title {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            color: #fff;
            font-size: 48px;
            font-weight: bold;
            text-align: center;
            text-shadow: 0 0 20px #ffff00, 0 0 40px #ffaa00;
            pointer-events: none;
            z-index: 10;
            animation: pulse 2s ease-in-out infinite;
        }

        @keyframes pulse {
            0%, 100% { opacity: 0.8; }
            50% { opacity: 1; }
        }
    </style>
</head>
<body>
    <canvas id="starfield"></canvas>
    <div class="title">SWIFT ⭐</div>

    <script>
        const canvas = document.getElementById('starfield');
        const ctx = canvas.getContext('2d');

        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;

        window.addEventListener('resize', () => {
            canvas.width = window.innerWidth;
            canvas.height = window.innerHeight;
        });

        class Star {
            constructor() {
                this.reset();
                // Start at random positions for initial spread
                this.x = Math.random() * canvas.width;
                this.y = Math.random() * canvas.height;
            }

            reset() {
                // Stars shoot from center
                this.x = canvas.width / 2;
                this.y = canvas.height / 2;

                // Random angle for Swift-like spread
                this.angle = Math.random() * Math.PI * 2;
                this.speed = 2 + Math.random() * 4;

                // Velocity components
                this.vx = Math.cos(this.angle) * this.speed;
                this.vy = Math.sin(this.angle) * this.speed;

                // Star properties
                this.size = 8 + Math.random() * 12;
                this.rotation = Math.random() * Math.PI * 2;
                this.rotationSpeed = (Math.random() - 0.5) * 0.2;

                // Color variations (yellow/gold like Pokemon Swift)
                const colorVariation = Math.random();
                if (colorVariation < 0.6) {
                    this.color = '#ffff00'; // Yellow
                } else if (colorVariation < 0.9) {
                    this.color = '#ffaa00'; // Orange-yellow
                } else {
                    this.color = '#ffffff'; // White
                }

                this.alpha = 1;
                this.fadeSpeed = 0.005 + Math.random() * 0.01;
                this.trail = [];
            }

            update() {
                // Add current position to trail
                this.trail.push({ x: this.x, y: this.y, alpha: this.alpha });
                if (this.trail.length > 8) {
                    this.trail.shift();
                }

                // Update position
                this.x += this.vx;
                this.y += this.vy;

                // Update rotation
                this.rotation += this.rotationSpeed;

                // Gradually fade out
                this.alpha -= this.fadeSpeed;

                // Reset if star is off screen or fully faded
                if (this.x < -50 || this.x > canvas.width + 50 ||
                    this.y < -50 || this.y > canvas.height + 50 ||
                    this.alpha <= 0) {
                    this.reset();
                }
            }

            drawStar(x, y, spikes, outerRadius, innerRadius, rotation, alpha) {
                ctx.save();
                ctx.translate(x, y);
                ctx.rotate(rotation);

                ctx.beginPath();
                for (let i = 0; i < spikes * 2; i++) {
                    const radius = i % 2 === 0 ? outerRadius : innerRadius;
                    const angle = (Math.PI / spikes) * i;
                    const px = Math.cos(angle) * radius;
                    const py = Math.sin(angle) * radius;

                    if (i === 0) {
                        ctx.moveTo(px, py);
                    } else {
                        ctx.lineTo(px, py);
                    }
                }
                ctx.closePath();

                // Glow effect
                ctx.shadowBlur = 20;
                ctx.shadowColor = this.color;
                ctx.fillStyle = this.color;
                ctx.globalAlpha = alpha;
                ctx.fill();

                // Inner glow
                ctx.shadowBlur = 10;
                ctx.fillStyle = '#ffffff';
                ctx.globalAlpha = alpha * 0.6;
                ctx.fill();

                ctx.restore();
            }

            draw() {
                // Draw trail
                this.trail.forEach((point, index) => {
                    const trailAlpha = (index / this.trail.length) * this.alpha * 0.4;
                    const trailSize = this.size * (index / this.trail.length) * 0.7;

                    ctx.globalAlpha = trailAlpha;
                    ctx.fillStyle = this.color;
                    ctx.shadowBlur = 10;
                    ctx.shadowColor = this.color;
                    ctx.beginPath();
                    ctx.arc(point.x, point.y, trailSize / 3, 0, Math.PI * 2);
                    ctx.fill();
                });

                // Draw main star
                this.drawStar(
                    this.x,
                    this.y,
                    5, // 5-pointed star
                    this.size,
                    this.size * 0.4,
                    this.rotation,
                    this.alpha
                );
            }
        }

        // Create stars
        const stars = [];
        const starCount = 80;

        for (let i = 0; i < starCount; i++) {
            stars.push(new Star());
        }

        // Animation loop
        function animate() {
            // Fade effect instead of clearing
            ctx.fillStyle = 'rgba(10, 10, 26, 0.15)';
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            // Update and draw stars
            stars.forEach(star => {
                star.update();
                star.draw();
            });

            requestAnimationFrame(animate);
        }

        animate();

        // Add stars on click
        canvas.addEventListener('click', (e) => {
            for (let i = 0; i < 20; i++) {
                const star = new Star();
                star.x = e.clientX;
                star.y = e.clientY;
                stars.push(star);
            }

            // Remove excess stars
            if (stars.length > 200) {
                stars.splice(0, stars.length - 200);
            }
        });
    </script>
</body>
</html>
```
