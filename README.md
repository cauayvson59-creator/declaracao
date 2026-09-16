<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Declaração Especial</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        body, html {
            width: 100%;
            height: 100%;
            overflow: hidden;
            background-color: #b30000;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            display: flex;
            justify-content: center;
            align-items: center;
        }
        canvas {
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: 1;
        }

        /* Container da Música no Topo */
        .music-container {
            position: absolute;
            top: 15px;
            z-index: 10;
            display: flex;
            flex-direction: column;
            align-items: center;
            background: rgba(255, 255, 255, 0.2);
            backdrop-filter: blur(10px);
            padding: 8px 12px;
            border-radius: 15px;
            border: 1px solid rgba(255, 255, 255, 0.4);
            box-shadow: 0 4px 15px rgba(0,0,0,0.3);
        }

        .music-container span {
            color: white;
            font-size: 12px;
            font-weight: bold;
            margin-bottom: 4px;
            text-shadow: 1px 1px 2px rgba(0,0,0,0.4);
        }

        .player-wrapper {
            position: relative;
            width: 160px;
            height: 90px;
            border-radius: 8px;
            overflow: hidden;
            border: 2px solid rgba(255, 255, 255, 0.6);
        }

        .player-wrapper iframe {
            width: 100%;
            height: 100%;
            border: none;
        }

        /* Container centralizado */
        .center-wrapper {
            position: relative;
            z-index: 3;
            display: flex;
            justify-content: center;
            align-items: center;
            width: 100%;
            height: 100%;
            padding: 15px;
        }

        /* Tela Inicial / Botão de Entrada */
        .welcome-container {
            text-align: center;
            background: rgba(255, 255, 255, 0.15);
            backdrop-filter: blur(12px);
            -webkit-backdrop-filter: blur(12px);
            padding: 30px 40px;
            border-radius: 20px;
            border: 1px solid rgba(255, 255, 255, 0.4);
            box-shadow: 0 15px 35px rgba(0, 0, 0, 0.3);
            transition: opacity 0.5s ease, transform 0.5s ease;
            position: absolute;
            margin-top: 60px;
        }

        .welcome-container h1 {
            color: #ffffff;
            font-size: 22px;
            margin-bottom: 20px;
            text-shadow: 1px 1px 3px rgba(0, 0, 0, 0.4);
        }

        .welcome-btn {
            background-color: #ffffff;
            color: #b30000;
            border: none;
            padding: 12px 25px;
            font-size: 16px;
            font-weight: bold;
            border-radius: 30px;
            cursor: pointer;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.2);
            transition: transform 0.2s ease, background-color 0.2s ease;
        }

        .welcome-btn:hover {
            transform: scale(1.05);
            background-color: #f0f0f0;
        }

        /* Coração Vermelho de Explosão ao Clicar */
        .heart-burst {
            position: absolute;
            font-size: 0px;
            color: #ff1a1a;
            z-index: 4;
            pointer-events: none;
            transform: translate(-50%, -50%);
            transition: transform 0.6s cubic-bezier(0.175, 0.885, 0.32, 1.275), opacity 0.6s ease;
            opacity: 0;
            text-shadow: 0 0 20px rgba(255, 0, 0, 0.8);
        }

        .heart-burst.animar {
            font-size: 180px;
            opacity: 1;
            transform: translate(-50%, -50%) scale(1.5);
        }

        /* Caixa da mensagem centralizada corrigida */
        .mensagem-container {
            position: absolute;
            width: 88%;
            max-width: 500px;
            height: 62vh; /* Altura controlada para caber na tela do celular */
            overflow-y: auto;
            background: rgba(255, 255, 255, 0.18);
            backdrop-filter: blur(12px);
            -webkit-backdrop-filter: blur(12px);
            padding: 25px 20px;
            border-radius: 20px;
            border: 1px solid rgba(255, 255, 255, 0.4);
            box-shadow: 0 15px 35px rgba(0, 0, 0, 0.3);
            color: #ffffff;
            text-align: left;
            opacity: 0;
            transform: translateY(30px) scale(0.95);
            pointer-events: none;
            transition: opacity 0.8s cubic-bezier(0.16, 1, 0.3, 1) 0.3s, transform 0.8s cubic-bezier(0.16, 1, 0.3, 1) 0.3s;
            margin-top: 70px;
        }

        .mensagem-container.mostrar {
            opacity: 1;
            transform: translateY(0) scale(1);
            pointer-events: auto;
        }

        .welcome-container.esconder {
            opacity: 0;
            transform: scale(0.8);
            pointer-events: none;
        }

        .mensagem-container::-webkit-scrollbar {
            width: 5px;
        }
        .mensagem-container::-webkit-scrollbar-thumb {
            background: rgba(255, 255, 255, 0.4);
            border-radius: 10px;
        }

        .mensagem-container p {
            font-size: 14px;
            line-height: 1.5;
            margin-bottom: 14px;
            word-wrap: break-word; /* Garante que as palavras quebrem direito */
            text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.4);
        }
        .mensagem-container p:last-child {
            margin-bottom: 0;
        }
        
        .credito {
            position: absolute;
            bottom: 10px;
            right: 15px;
            color: rgba(255, 255, 255, 0.8);
            font-size: 13px;
            font-weight: bold;
            text-shadow: 1px 1px 3px rgba(0, 0, 0, 0.5);
            z-index: 5;
            pointer-events: none;
        }
    </style>
</head>
<body>

    <canvas id="canvas"></canvas>

    <div class="music-container">
        <span>🎵 Música</span>
        <div class="player-wrapper">
            <iframe src="https://www.youtube.com/embed/QpQ2_E4e3lQ?autoplay=1&loop=1&playlist=QpQ2_E4e3lQ" title="YouTube video player" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture"></iframe>
        </div>
    </div>

    <div class="center-wrapper">
        <div class="welcome-container" id="welcomeBox">
            <h1>Olá, isso é para você</h1>
            <button class="welcome-btn" onclick="abrirDeclaracao()">Abrir Mensagem</button>
        </div>

        <div class="heart-burst" id="heartBurst">❤</div>

        <div class="mensagem-container" id="mensagemBox">
            <p>Oi, sei que o tempo passou e que nossas vidas tomaram rumos diferentes, mas há algo que ficou guardado aqui e que eu precisava te dizer de verdade, com o coração aberto.</p>
            <p>Na época em que a gente tinha 15, 16 anos, eu já gostava muito de você. Você sempre foi uma garota incrível, linda de um jeito único e extremamente dedicada aos estudos — o que sempre me fez te admirar ainda mais. Quando tomei coragem e tentei me declarar para você lá atrás, as coisas não saíram como eu esperava e você acabou contando para a minha mãe. Naquele momento, confesso que fiquei muito chateado e para baixo, porque era um sentimento muito sincero da minha parte e eu não sabia lidar muito bem com a situação.</p>
            <p>A verdade é que o tempo passou, mas a admiração nunca sumiu de vez. Para falar a verdade, até hoje, toda vez que vejo suas fotos, fico te olhando e lembrando de tudo.</p>
            <p>Escrevo isso não para cobrar nada do passado, mas porque precisava tirar isso de dentro de mim e te dizer o quanto você é especial, além de te desejar todo o sucesso do mundo na sua caminhada.</p>
        </div>
    </div>

    <div class="credito">Criado por Kauan</div>

    <script>
        function abrirDeclaracao() {
            const welcome = document.getElementById('welcomeBox');
            const heart = document.getElementById('heartBurst');
            const mensagem = document.getElementById('mensagemBox');

            welcome.classList.add('esconder');
            heart.classList.add('animar');

            setTimeout(() => {
                welcome.style.display = 'none';
                heart.style.opacity = '0';
                mensagem.classList.add('mostrar');
            }, 500);
        }

        const canvas = document.getElementById('canvas');
        const ctx = canvas.getContext('2d');

        function resize() {
            canvas.width = window.innerWidth;
            canvas.height = window.innerHeight;
        }
        window.addEventListener('resize', resize);
        resize();

        const BLUE_SHADES = ["#3b82f6", "#60a5fa", "#93c5fd", "#2563eb", "#bfdbfe", "#1d4ed8"];

        class Coracao {
            constructor() {
                this.reset();
            }

            reset() {
                this.x = Math.random() * canvas.width;
                this.y = canvas.height + Math.random() * 100;
                this.size = Math.random() * 14 + 8;
                this.color = BLUE_SHADES[Math.floor(Math.random() * BLUE_SHADES.length)];
                
                this.angle = Math.random() * Math.PI * 2;
                this.maxSwing = Math.random() * 0.4 + 0.3; 
                this.baseAngle = this.angle;
                this.swingTime = Math.random() * 100;
                
                this.speedX = Math.random() * 0.4 - 0.2;
                this.speedY = Math.random() * -0.8 - 0.3;
                this.alpha = Math.random() * 0.5 + 0.5;
            }

            update() {
                this.x += this.speedX;
                this.y += this.speedY;

                this.swingTime += 0.02; 
                this.angle = this.baseAngle + Math.sin(this.swingTime) * this.maxSwing;

                if (this.y < -50) {
                    this.reset();
                }
                if (this.x < -50) this.x = canvas.width + 50;
                if (this.x > canvas.width + 50) this.x = -50;
            }

            draw() {
                ctx.save();
                ctx.translate(this.x, this.y);
                ctx.rotate(this.angle);
                ctx.globalAlpha = this.alpha;
                
                ctx.shadowColor = '#60a5fa';
                ctx.shadowBlur = 10;

                ctx.fillStyle = this.color;
                ctx.beginPath();
                ctx.moveTo(0, 0);
                ctx.bezierCurveTo(-this.size / 2, -this.size / 2, -this.size, +this.size / 3, 0, this.size);
                ctx.bezierCurveTo(this.size, +this.size / 3, this.size / 2, -this.size / 2, 0, 0);
                ctx.fill();
                
                ctx.restore();
            }
        }

        class Lirio {
            constructor() {
                this.reset();
            }

            reset() {
                this.x = Math.random() * canvas.width;
                this.y = canvas.height + Math.random() * 100;
                this.size = Math.random() * 12 + 10;
                this.speedY = Math.random() * -0.6 - 0.2;
                this.speedX = Math.random() * 0.6 - 0.3;
                this.angle = Math.random() * Math.PI * 2;
                this.spin = Math.random() * 0.02 - 0.01;
                this.alpha = Math.random() * 0.4 + 0.3;
            }

            update() {
                this.y += this.speedY;
                this.x += this.speedX;
                this.angle += this.spin;

                if (this.y < -50) {
                    this.reset();
                }
            }

            draw() {
                ctx.save();
                ctx.translate(this.x, this.y);
                ctx.rotate(this.angle);
                ctx.globalAlpha = this.alpha;

                ctx.fillStyle = '#ffffff';
                ctx.shadowColor = '#ffccd5';
                ctx.shadowBlur = 8;

                for (let i = 0; i < 5; i++) {
                    ctx.rotate((Math.PI * 2) / 5);
                    ctx.beginPath();
                    ctx.ellipse(0, -this.size / 2, this.size / 3, this.size, 0, 0, Math.PI * 2);
                    ctx.fill();
                }

                ctx.fillStyle = '#ffdfba';
                ctx.beginPath();
                ctx.arc(0, 0, this.size / 4, 0, Math.PI * 2);
                ctx.fill();

                ctx.restore();
            }
        }

        const coracoes = [];
        for (let i = 0; i < 40; i++) {
            coracoes.push(new Coracao());
        }

        const lirios = [];
        for (let i = 0; i < 15; i++) {
            lirios.push(new Lirio());
        }

        function animate() {
            ctx.fillStyle = '#b30000';
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            coracoes.forEach(coracao => {
                coracao.update();
                coracao.draw();
            });

            lirios.forEach(lirio => {
                lirio.update();
                lirio.draw();
            });

            requestAnimationFrame(animate);
        }

        animate();
    </script>
</body>
</html>
