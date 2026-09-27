
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Matriz de Aproveitamento Geotérmico e Cinético Profundo</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            user-select: none;
            font-family: 'Segoe UI', system-ui, -apple-system, sans-serif;
        }

        body {
            background-color: #03060d;
            color: #d1edff;
            overflow: hidden;
            display: flex;
            height: 100vh;
            width: 100vw;
        }

        #app-container {
            display: flex;
            width: 100%;
            height: 100%;
            position: relative;
        }

        #canvas-wrapper {
            flex: 1;
            position: relative;
            height: 100%;
            background: radial-gradient(circle at 50% 50%, #08101e 0%, #020408 100%);
        }

        canvas {
            display: block;
            width: 100%;
            height: 100%;
        }

        /* HUD Superior sobre o Canvas */
        .canvas-hud {
            position: absolute;
            top: 16px;
            left: 20px;
            display: flex;
            gap: 16px;
            z-index: 5;
            pointer-events: none;
        }

        .hud-badge {
            background: rgba(6, 15, 28, 0.75);
            border: 1px solid rgba(0, 242, 254, 0.3);
            backdrop-filter: blur(8px);
            border-radius: 8px;
            padding: 10px 16px;
            box-shadow: 0 4px 20px rgba(0, 0, 0, 0.5);
        }

        .hud-badge .title {
            font-size: 0.65rem;
            text-transform: uppercase;
            letter-spacing: 1px;
            color: #64748b;
        }

        .hud-badge .value {
            font-size: 1.2rem;
            font-weight: 700;
            font-family: 'Courier New', Courier, monospace;
            color: #00f2fe;
            text-shadow: 0 0 10px rgba(0, 242, 254, 0.5);
        }

        /* Painel Lateral de Controle Sci-Fi */
        #ui-panel {
            width: 380px;
            background: rgba(5, 12, 22, 0.92);
            backdrop-filter: blur(16px);
            border-left: 1px solid rgba(0, 242, 254, 0.2);
            padding: 20px;
            display: flex;
            flex-direction: column;
            gap: 16px;
            box-shadow: -10px 0 35px rgba(0, 0, 0, 0.8);
            z-index: 10;
            overflow-y: auto;
        }

        #ui-panel::-webkit-scrollbar {
            width: 4px;
        }
        #ui-panel::-webkit-scrollbar-thumb {
            background: rgba(0, 242, 254, 0.3);
            border-radius: 2px;
        }

        .panel-header {
            border-bottom: 1px solid rgba(0, 242, 254, 0.25);
            padding-bottom: 10px;
        }

        .panel-header h1 {
            font-size: 1.1rem;
            font-weight: 800;
            letter-spacing: 1.5px;
            text-transform: uppercase;
            background: linear-gradient(90deg, #ff6b00, #00f2fe);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }

        .panel-header p {
            font-size: 0.72rem;
            color: #94a3b8;
            margin-top: 3px;
        }

        /* Cartões de Métricas */
        .metrics-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 10px;
        }

        .metric-card {
            background: rgba(255, 255, 255, 0.025);
            border: 1px solid rgba(255, 255, 255, 0.07);
            border-radius: 8px;
            padding: 10px 12px;
            transition: all 0.3s ease;
        }

        .metric-card.highlight {
            grid-column: span 2;
            background: rgba(0, 242, 254, 0.05);
            border-color: rgba(0, 242, 254, 0.3);
        }

        .metric-title {
            font-size: 0.68rem;
            color: #818cf8;
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }

        .metric-value {
            font-size: 1.3rem;
            font-weight: 800;
            font-family: 'Courier New', Courier, monospace;
            color: #ffffff;
            margin-top: 2px;
        }

        .metric-card.highlight .metric-value {
            font-size: 1.8rem;
            color: #38ef7d;
            text-shadow: 0 0 12px rgba(56, 239, 125, 0.4);
        }

        .metric-sub {
            font-size: 0.68rem;
            color: #94a3b8;
            margin-top: 2px;
        }

        /* Seção de Controles */
        .section-title {
            font-size: 0.75rem;
            font-weight: 700;
            color: #38bdf8;
            text-transform: uppercase;
            letter-spacing: 1px;
            margin-top: 4px;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .section-title::after {
            content: '';
            flex: 1;
            height: 1px;
            background: rgba(56, 189, 248, 0.2);
        }

        .control-group {
            display: flex;
            flex-direction: column;
            gap: 6px;
        }

        .control-label {
            font-size: 0.75rem;
            color: #cbd5e1;
            display: flex;
            justify-content: space-between;
        }

        .control-label span {
            font-family: 'Courier New', Courier, monospace;
            color: #00f2fe;
            font-weight: bold;
        }

        input[type="range"] {
            width: 100%;
            height: 5px;
            border-radius: 3px;
            background: #1e293b;
            outline: none;
            accent-color: #00f2fe;
            cursor: pointer;
        }

        /* Botões Ação / Toggle */
        .action-buttons {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 10px;
        }

        .btn {
            background: rgba(0, 242, 254, 0.08);
            border: 1px solid rgba(0, 242, 254, 0.3);
            color: #00f2fe;
            padding: 10px;
            border-radius: 6px;
            font-size: 0.72rem;
            font-weight: 700;
            cursor: pointer;
            transition: all 0.25s ease;
            text-transform: uppercase;
            letter-spacing: 0.5px;
            display: flex;
            align-items: center;
            justify-content: center;
            gap: 6px;
            text-align: center;
        }

        .btn:hover {
            background: rgba(0, 242, 254, 0.25);
            box-shadow: 0 0 15px rgba(0, 242, 254, 0.3);
            border-color: #00f2fe;
        }

        .btn-magma {
            background: rgba(255, 107, 0, 0.1);
            border-color: rgba(255, 107, 0, 0.4);
            color: #ff6b00;
        }

        .btn-magma:hover {
            background: rgba(255, 107, 0, 0.28);
            box-shadow: 0 0 15px rgba(255, 107, 0, 0.4);
            border-color: #ff6b00;
        }

        .btn-seismic {
            background: rgba(168, 85, 247, 0.1);
            border-color: rgba(168, 85, 247, 0.4);
            color: #c084fc;
        }

        .btn-seismic:hover {
            background: rgba(168, 85, 247, 0.28);
            box-shadow: 0 0 15px rgba(168, 85, 247, 0.4);
            border-color: #c084fc;
        }

        .toggle-group {
            display: flex;
            flex-direction: column;
            gap: 8px;
        }

        .toggle-btn {
            display: flex;
            align-items: center;
            justify-content: space-between;
            padding: 8px 12px;
            background: rgba(255, 255, 255, 0.03);
            border: 1px solid rgba(255, 255, 255, 0.1);
            border-radius: 6px;
            cursor: pointer;
            font-size: 0.74rem;
            color: #94a3b8;
            transition: all 0.2s;
        }

        .toggle-btn.active {
            background: rgba(56, 239, 125, 0.08);
            border-color: rgba(56, 239, 125, 0.4);
            color: #38ef7d;
        }

        .toggle-indicator {
            width: 8px;
            height: 8px;
            border-radius: 50%;
            background: #475569;
            box-shadow: 0 0 0 rgba(0,0,0,0);
            transition: all 0.2s;
        }

        .toggle-btn.active .toggle-indicator {
            background: #38ef7d;
            box-shadow: 0 0 8px #38ef7d;
        }

        /* Legenda */
        .legend-box {
            background: rgba(0, 0, 0, 0.3);
            border: 1px solid rgba(255, 255, 255, 0.05);
            border-radius: 6px;
            padding: 10px;
            font-size: 0.68rem;
            display: flex;
            flex-direction: column;
            gap: 6px;
        }

        .legend-item {
            display: flex;
            align-items: center;
            gap: 8px;
            color: #cbd5e1;
        }

        .dot {
            width: 8px;
            height: 8px;
            border-radius: 50%;
        }

        .dot-magma { background: #ff4500; box-shadow: 0 0 6px #ff4500; }
        .dot-seismic { background: #a855f7; box-shadow: 0 0 6px #a855f7; }
        .dot-hydro { background: #00f2fe; box-shadow: 0 0 6px #00f2fe; }
        .dot-grid { background: #38ef7d; box-shadow: 0 0 6px #38ef7d; }

        @media (max-width: 900px) {
            body {
                flex-direction: column;
            }
            #ui-panel {
                width: 100%;
                height: 45vh;
            }
            #canvas-wrapper {
                height: 55vh;
            }
        }
    </style>
</head>
<body>
    <div id="app-container">
        <!-- ÁREA DO CANVAS -->
        <div id="canvas-wrapper">
            <canvas id="simCanvas"></canvas>
            
            <!-- HUD de Status em Tempo Real -->
            <div class="canvas-hud">
                <div class="hud-badge">
                    <div class="title">Status do Core Tectônico</div>
                    <div class="value" id="hudStatus">OPERAÇÃO NOMINAL</div>
                </div>
                <div class="hud-badge">
                    <div class="title">Rede Global Mantida</div>
                    <div class="value" id="hudFrequency">60.00 Hz</div>
                </div>
            </div>
        </div>

        <!-- PAINEL DE CONTROLE LATERAL -->
        <div id="ui-panel">
            <div class="panel-header">
                <h1>GEO-KINETIC HARVESTER</h1>
                <p>Extração Geotérmica & Cinética Direta da Litosfera</p>
            </div>

            <!-- QUADRO DE MÉTRICAS -->
            <div class="metrics-grid">
                <div class="metric-card highlight">
                    <div class="metric-title">Geração Total Integrada</div>
                    <div class="metric-value" id="valTotalPower">18.42 TW</div>
                    <div class="metric-sub">Capacidade da Matriz Terrestre</div>
                </div>

                <div class="metric-card">
                    <div class="metric-title">Termo-Magmática (MHD)</div>
                    <div class="metric-value" id="valMagmaPower">10.25 TW</div>
                    <div class="metric-sub" id="valMagmaTemp">3.850 °C</div>
                </div>

                <div class="metric-card">
                    <div class="metric-title">Cinética Terremotos</div>
                    <div class="metric-value" id="valSeismicPower">4.18 TW</div>
                    <div class="metric-sub" id="valSeismicMag">Mag 4.2 Mw</div>
                </div>

                <div class="metric-card">
                    <div class="metric-title">Hidrocinética Oceânica</div>
                    <div class="metric-value" id="valHydroPower">3.99 TW</div>
                    <div class="metric-sub" id="valOceanSpeed">4.2 m/s</div>
                </div>

                <div class="metric-card">
                    <div class="metric-title">Estabilização Falha</div>
                    <div class="metric-value" id="valStabilization">96.4 %</div>
                    <div class="metric-sub">Dissipação de Tensão</div>
                </div>

                <div class="metric-card">
                    <div class="metric-title">Eficiência Térmica</div>
                    <div class="metric-value" id="valEfficiency">94.8 %</div>
                    <div class="metric-sub">Ciclo Supercondutor</div>
                </div>
            </div>

            <!-- CONTROLES DE SUBSISTEMAS -->
            <div class="section-title">Subsistemas de Captação</div>
            <div class="toggle-group">
                <div class="toggle-btn active" id="toggleMagma">
                    <span>Sondas Magnetohidrodinâmicas Profundas</span>
                    <div class="toggle-indicator"></div>
                </div>
                <div class="toggle-btn active" id="toggleSeismic">
                    <span>Absorvedores Piezotérmicos Tectônicos</span>
                    <div class="toggle-indicator"></div>
                </div>
                <div class="toggle-btn active" id="toggleHydro">
                    <span>Turbinas Hidrocinéticas Bentônicas</span>
                    <div class="toggle-indicator"></div>
                </div>
            </div>

            <!-- SLIDERS PARÂMETROS -->
            <div class="section-title">Parâmetros Geodinâmicos</div>
            
            <div class="control-group">
                <div class="control-label">Fluxo Térmico Magmático <span id="lblMagmaFlux">3.800 °C</span></div>
                <input type="range" id="slideMagma" min="1000" max="6000" value="3800">
            </div>

            <div class="control-group">
                <div class="control-label">Estresse Tectônico Acumulado <span id="lblSeismicStress">68%</span></div>
                <input type="range" id="slideSeismic" min="10" max="100" value="68">
            </div>

            <div class="control-group">
                <div class="control-label">Velocidade Corrente Profunda <span id="lblOceanSpeed">4.2 m/s</span></div>
                <input type="range" id="slideHydro" min="0.5" max="10.0" step="0.1" value="4.2">
            </div>

            <!-- BOTÕES DE AÇÃO DIRECTA -->
            <div class="section-title">Ações do Núcleo</div>
            <div class="action-buttons">
                <button class="btn btn-magma" id="btnMagmaPulse">
                    ⚡ Pulso Magmático
                </button>
                <button class="btn btn-seismic" id="btnInduceSeismic">
                    💥 Induzir Sismo
                </button>
            </div>

            <!-- LEGENDA -->
            <div class="legend-box">
                <div class="legend-item"><div class="dot dot-magma"></div> Magma Incandescente & Geradores MHD</div>
                <div class="legend-item"><div class="dot dot-seismic"></div> Falha Tectônica e Absorvedores Piezoelétricos</div>
                <div class="legend-item"><div class="dot dot-hydro"></div> Corrente Marinha Bentônica e Turbinas de Fossa</div>
                <div class="legend-item"><div class="dot dot-grid"></div> Transmissão de Energia para Superfície / Cidade</div>
            </div>
        </div>
    </div>

    <script>
        const canvas = document.getElementById('simCanvas');
        const ctx = canvas.getContext('2d');

        function resizeCanvas() {
            canvas.width = canvas.parentElement.clientWidth;
            canvas.height = canvas.parentElement.clientHeight;
        }
        window.addEventListener('resize', resizeCanvas);
        resizeCanvas();

        // Estado do Sistema Geodinâmico
        const simState = {
            // Toggles de Subsistemas
            magmaActive: true,
            seismicActive: true,
            hydroActive: true,

            // Parâmetros Ajustáveis
            magmaTemp: 3800, // °C
            seismicStress: 68, // %
            oceanSpeed: 4.2, // m/s

            // Pulsos Temporários
            magmaPulseTimer: 0,
            
            // Entidades e Partículas
            magmaParticles: [],
            seismicWaves: [],
            waterParticles: [],
            energyPulses: [],
            
            // Medidores Calculados
            magmaPower: 10.25,
            seismicPower: 4.18,
            hydroPower: 3.99,
            totalPower: 18.42,
            stabilizationPct: 96.4,
            efficiencyPct: 94.8,

            // Animações
            turbineAngle: 0,
            time: 0
        };

        const slideMagma = document.getElementById('slideMagma');
        const slideSeismic = document.getElementById('slideSeismic');
        const slideHydro = document.getElementById('slideHydro');

        const toggleMagma = document.getElementById('toggleMagma');
        const toggleSeismic = document.getElementById('toggleSeismic');
        const toggleHydro = document.getElementById('toggleHydro');

        const btnMagmaPulse = document.getElementById('btnMagmaPulse');
        const btnInduceSeismic = document.getElementById('btnInduceSeismic');

        // Sliders
        slideMagma.addEventListener('input', (e) => {
            simState.magmaTemp = parseFloat(e.target.value);
            document.getElementById('lblMagmaFlux').innerText = `${simState.magmaTemp} °C`;
        });

        slideSeismic.addEventListener('input', (e) => {
            simState.seismicStress = parseFloat(e.target.value);
            document.getElementById('lblSeismicStress').innerText = `${simState.seismicStress}%`;
        });

        slideHydro.addEventListener('input', (e) => {
            simState.oceanSpeed = parseFloat(e.target.value);
            document.getElementById('lblOceanSpeed').innerText = `${simState.oceanSpeed.toFixed(1)} m/s`;
        });

        // Toggles
        toggleMagma.addEventListener('click', () => {
            simState.magmaActive = !simState.magmaActive;
            toggleMagma.classList.toggle('active', simState.magmaActive);
        });

        toggleSeismic.addEventListener('click', () => {
            simState.seismicActive = !simState.seismicActive;
            toggleSeismic.classList.toggle('active', simState.seismicActive);
        });

        toggleHydro.addEventListener('click', () => {
            simState.hydroActive = !simState.hydroActive;
            toggleHydro.classList.toggle('active', simState.hydroActive);
        });

        // Botões de Ação
        btnMagmaPulse.addEventListener('click', () => {
            simState.magmaPulseTimer = 180; // 3 segundos de pulso térmico intenso
            triggerEnergyPulse('magma');
        });

        btnInduceSeismic.addEventListener('click', () => {
            triggerSeismicEvent();
        });

        function initParticles() {
            // Partículas da câmara de magma
            simState.magmaParticles = [];
            for (let i = 0; i < 90; i++) {
                simState.magmaParticles.push({
                    x: Math.random(),
                    y: Math.random(),
                    size: 2 + Math.random() * 6,
                    speedY: -0.001 - Math.random() * 0.002,
                    speedX: (Math.random() - 0.5) * 0.001,
                    hue: 10 + Math.random() * 30, // Laranja a Amarelo incandescente
                    alpha: 0.4 + Math.random() * 0.6
                });
            }

            // Partículas da corrente oceânica profunda
            simState.waterParticles = [];
            for (let i = 0; i < 60; i++) {
                simState.waterParticles.push({
                    x: Math.random(),
                    y: Math.random(),
                    length: 10 + Math.random() * 25,
                    speed: 0.002 + Math.random() * 0.004
                });
            }
        }

        function triggerSeismicEvent() {
            const faultX = canvas.width * 0.52;
            const faultY = canvas.height * 0.58;

            simState.seismicWaves.push({
                x: faultX,
                y: faultY,
                radius: 10,
                maxRadius: canvas.width * 0.35,
                intensity: 1.0,
                color: '#a855f7'
            });

            // Disparar pulso de energia nos absorvedores
            triggerEnergyPulse('seismic');
        }

        function triggerEnergyPulse(source) {
            const w = canvas.width;
            const h = canvas.height;

            let startX, startY;
            if (source === 'magma') {
                startX = w * 0.28;
                startY = h * 0.72;
            } else if (source === 'seismic') {
                startX = w * 0.52;
                startY = h * 0.58;
            } else {
                startX = w * 0.82;
                startY = h * 0.38;
            }

            // Destino: Cidade na superfície
            const endX = w * 0.45;
            const endY = h * 0.12;

            simState.energyPulses.push({
                x: startX,
                y: startY,
                startX: startX,
                startY: startY,
                endX: endX,
                endY: endY,
                progress: 0,
                color: source === 'magma' ? '#ff6b00' : (source === 'seismic' ? '#c084fc' : '#00f2fe')
            });
        }

        function drawGeologicalCrossSection() {
            const w = canvas.width;
            const h = canvas.height;

            // 1. ESPAÇO E ATMOSFERA SUPERIOR
            const skyGrad = ctx.createLinearGradient(0, 0, 0, h * 0.15);
            skyGrad.addColorStop(0, '#02040a');
            skyGrad.addColorStop(1, '#091528');
            ctx.fillStyle = skyGrad;
            ctx.fillRect(0, 0, w, h * 0.15);

            // 2. OCEANO PROFUNDO E FOSSA BENTÔNICA (Lado Direito)
            ctx.save();
            ctx.beginPath();
            ctx.moveTo(w * 0.65, h * 0.15);
            ctx.lineTo(w, h * 0.15);
            ctx.lineTo(w, h * 0.48);
            ctx.lineTo(w * 0.72, h * 0.48);
            ctx.lineTo(w * 0.65, h * 0.15);
            ctx.closePath();

            const oceanGrad = ctx.createLinearGradient(w * 0.65, h * 0.15, w, h * 0.48);
            oceanGrad.addColorStop(0, 'rgba(0, 80, 160, 0.75)');
            oceanGrad.addColorStop(1, 'rgba(1, 18, 42, 0.95)');
            ctx.fillStyle = oceanGrad;
            ctx.fill();

            // Raios solares subaquáticos atenuados
            ctx.strokeStyle = 'rgba(0, 242, 254, 0.08)';
            ctx.lineWidth = 15;
            for (let i = 0; i < 4; i++) {
                ctx.beginPath();
                ctx.moveTo(w * 0.68 + i * 40, h * 0.15);
                ctx.lineTo(w * 0.72 + i * 50, h * 0.35);
                ctx.stroke();
            }
            ctx.restore();

            // 3. CAMADA TERRESTRE / ROCAS DA CROSTA E LITOSFERA
            ctx.save();
            // Rocha Superficial
            const crustGrad = ctx.createLinearGradient(0, h * 0.15, 0, h);
            crustGrad.addColorStop(0, '#151c28');
            crustGrad.addColorStop(0.35, '#0d131d');
            crustGrad.addColorStop(0.7, '#110d18');
            crustGrad.addColorStop(1, '#1d0806');

            ctx.fillStyle = crustGrad;
            ctx.beginPath();
            ctx.moveTo(0, h * 0.15);
            // Vulcão na esquerda
            ctx.lineTo(w * 0.12, h * 0.15);
            ctx.lineTo(w * 0.22, h * 0.08); // Cume vulcânico
            ctx.lineTo(w * 0.26, h * 0.15);
            // Planície da Cidade no centro
            ctx.lineTo(w * 0.65, h * 0.15);
            // Fossa bentônica no oceano
            ctx.lineTo(w * 0.72, h * 0.48);
            ctx.lineTo(w, h * 0.48);
            ctx.lineTo(w, h);
            ctx.lineTo(0, h);
            ctx.closePath();
            ctx.fill();

            // Linhas de Estratificação Rochosa
            ctx.strokeStyle = 'rgba(255, 255, 255, 0.04)';
            ctx.lineWidth = 2;
            for (let y = h * 0.22; y < h; y += 45) {
                ctx.beginPath();
                ctx.moveTo(0, y);
                ctx.lineTo(w * 0.65, y + Math.sin(y) * 15);
                ctx.stroke();
            }
            ctx.restore();

            // 4. FALHA TECTÔNICA (Linha diagonal de estresse)
            ctx.save();
            const faultX1 = w * 0.42;
            const faultY1 = h * 0.18;
            const faultX2 = w * 0.62;
            const faultY2 = h * 0.88;

            ctx.beginPath();
            ctx.moveTo(faultX1, faultY1);
            ctx.lineTo(faultX2, faultY2);
            
            // Brilho de tensão tectônica
            const stressRatio = simState.seismicStress / 100;
            ctx.strokeStyle = `rgba(168, 85, 247, ${0.2 + stressRatio * 0.7})`;
            ctx.lineWidth = 4 + stressRatio * 6;
            ctx.shadowColor = '#a855f7';
            ctx.shadowBlur = 15 * stressRatio;
            ctx.stroke();

            // Rótulo da Falha
            ctx.fillStyle = '#c084fc';
            ctx.font = '10px sans-serif';
            ctx.fillText("FALHA TECTÔNICA PROFUNDA", faultX1 + 15, faultY1 + 50);
            ctx.restore();
        }

        function drawMagmaChamber() {
            const w = canvas.width;
            const h = canvas.height;

            ctx.save();
            const chamberX = w * 0.22;
            const chamberY = h * 0.78;
            const radiusX = w * 0.18;
            const radiusY = h * 0.16;

            // Pulso do calor
            const pulse = Math.sin(simState.time * 0.05) * 5;
            const effectiveTemp = simState.magmaTemp + (simState.magmaPulseTimer > 0 ? 1500 : 0);

            // Gradiente incandescente da câmara
            const magmaGrad = ctx.createRadialGradient(
                chamberX, chamberY, 10,
                chamberX, chamberY, radiusX + pulse
            );
            
            const tempRatio = Math.min(1, effectiveTemp / 6000);
            magmaGrad.addColorStop(0, `rgba(255, 240, 150, ${0.9 + tempRatio * 0.1})`);
            magmaGrad.addColorStop(0.4, `rgba(255, 90, 0, ${0.85})`);
            magmaGrad.addColorStop(0.8, `rgba(180, 20, 0, 0.75)`);
            magmaGrad.addColorStop(1, 'rgba(40, 5, 0, 0)');

            ctx.beginPath();
            ctx.ellipse(chamberX, chamberY, radiusX + pulse, radiusY + pulse, 0, 0, Math.PI * 2);
            ctx.fillStyle = magmaGrad;
            ctx.fill();

            // Conduto Vulcânico que sobe ao cume
            ctx.beginPath();
            ctx.moveTo(chamberX - 25, chamberY);
            ctx.quadraticCurveTo(chamberX - 10, h * 0.4, w * 0.22, h * 0.08);
            ctx.lineTo(w * 0.22 + 20, h * 0.08);
            ctx.quadraticCurveTo(chamberX + 15, h * 0.4, chamberX + 25, chamberY);
            
            const conduitGrad = ctx.createLinearGradient(chamberX, chamberY, chamberX, h * 0.08);
            conduitGrad.addColorStop(0, 'rgba(255, 100, 0, 0.8)');
            conduitGrad.addColorStop(1, 'rgba(255, 30, 0, 0.2)');
            ctx.fillStyle = conduitGrad;
            ctx.fill();

            // Renderizar partículas fluidas de magma (bolhas e matéria em ebulição)
            simState.magmaParticles.forEach(p => {
                p.y += p.speedY * (1 + tempRatio);
                p.x += p.speedX;

                if (p.y < 0) p.y = 1;
                if (p.x < 0 || p.x > 1) p.x = Math.random();

                const px = chamberX - radiusX + p.x * (radiusX * 2);
                const py = chamberY - radiusY + p.y * (radiusY * 2);

                ctx.beginPath();
                ctx.arc(px, py, p.size, 0, Math.PI * 2);
                ctx.fillStyle = `hsla(${p.hue}, 100%, 60%, ${p.alpha})`;
                ctx.shadowColor = '#ff4500';
                ctx.shadowBlur = 8;
                ctx.fill();
            });

            ctx.restore();
        }

        function drawGeothermalProbes() {
            if (!simState.magmaActive) return;

            const w = canvas.width;
            const h = canvas.height;

            ctx.save();
            const probeX = w * 0.22;
            const probeStartY = h * 0.15;
            const probeEndY = h * 0.72;

            // Haste Principal da Sonda Supercondutora
            ctx.beginPath();
            ctx.moveTo(probeX, probeStartY);
            ctx.lineTo(probeX, probeEndY);
            ctx.lineWidth = 8;
            ctx.strokeStyle = '#334155';
            ctx.stroke();

            // Core Interno com Plasma Magnetohidrodinâmico (MHD)
            ctx.beginPath();
            ctx.moveTo(probeX, probeStartY);
            ctx.lineTo(probeX, probeEndY);
            ctx.lineWidth = 3;
            ctx.strokeStyle = '#ff6b00';
            ctx.shadowColor = '#ff6b00';
            ctx.shadowBlur = 12;
            ctx.stroke();

            // Cabeça Extratora da Sonda inserida no Magma
            ctx.beginPath();
            ctx.arc(probeX, probeEndY, 16, 0, Math.PI * 2);
            ctx.fillStyle = '#0f172a';
            ctx.strokeStyle = '#ff6b00';
            ctx.lineWidth = 3;
            ctx.fill();
            ctx.stroke();

            // Anéis Trocadores de Calor
            for (let y = probeStartY + 40; y < probeEndY - 20; y += 35) {
                ctx.fillStyle = '#00f2fe';
                ctx.fillRect(probeX - 8, y, 16, 4);
            }

            // Rótulo
            ctx.fillStyle = '#ff9e00';
            ctx.font = '10px monospace';
            ctx.fillText("SONDA MHD MAGMÁTICA Profundidade: 12.500m", probeX + 15, probeEndY - 20);

            ctx.restore();
        }

        function drawSeismicSystem() {
            const w = canvas.width;
            const h = canvas.height;

            ctx.save();
            // Posição dos nós absorvedores ao longo da falha
            const nodes = [
                { x: w * 0.46, y: h * 0.32 },
                { x: w * 0.52, y: h * 0.52 },
                { x: w * 0.58, y: h * 0.72 }
            ];

            // Renderizar Nós Absorvedores Piezoelétricos
            nodes.forEach((node, idx) => {
                ctx.beginPath();
                ctx.arc(node.x, node.y, 10, 0, Math.PI * 2);
                ctx.fillStyle = simState.seismicActive ? '#1e1b4b' : '#1e293b';
                ctx.strokeStyle = simState.seismicActive ? '#a855f7' : '#64748b';
                ctx.lineWidth = 2.5;
                ctx.fill();
                ctx.stroke();

                if (simState.seismicActive) {
                    ctx.beginPath();
                    ctx.arc(node.x, node.y, 4, 0, Math.PI * 2);
                    ctx.fillStyle = '#c084fc';
                    ctx.shadowColor = '#c084fc';
                    ctx.shadowBlur = 10;
                    ctx.fill();
                }
            });

            // Atualizar e desenhar Ondas Choque Sísmicas
            for (let i = simState.seismicWaves.length - 1; i >= 0; i--) {
                const wave = simState.seismicWaves[i];
                wave.radius += 2.5;
                wave.intensity -= 0.008;

                ctx.beginPath();
                ctx.arc(wave.x, wave.y, wave.radius, 0, Math.PI * 2);
                ctx.strokeStyle = `rgba(168, 85, 247, ${Math.max(0, wave.intensity)})`;
                ctx.lineWidth = 3;
                ctx.shadowColor = '#a855f7';
                ctx.shadowBlur = 15;
                ctx.stroke();

                if (wave.radius >= wave.maxRadius || wave.intensity <= 0) {
                    simState.seismicWaves.splice(i, 1);
                }
            }

            ctx.restore();
        }

        function drawBenthicHydroSystem() {
            const w = canvas.width;
            const h = canvas.height;

            ctx.save();
            const trenchX = w * 0.82;
            const trenchY = h * 0.38;

            // Renderizar Partículas de Água Fluindo (Corrente Profunda)
            ctx.strokeStyle = 'rgba(0, 242, 254, 0.25)';
            ctx.lineWidth = 1.5;

            simState.waterParticles.forEach(p => {
                p.x += p.speed * (simState.oceanSpeed / 4.2);
                if (p.x > 1) p.x = 0;

                const px = w * 0.65 + p.x * (w * 0.35);
                const py = h * 0.18 + p.y * (h * 0.28);

                ctx.beginPath();
                ctx.moveTo(px, py);
                ctx.lineTo(px + p.length, py);
                ctx.stroke();
            });

            // Desenhar Turbina Hidrocinética Submarina
            if (simState.hydroActive) {
                simState.turbineAngle += 0.02 * simState.oceanSpeed;
            }

            // Base da Turbina
            ctx.fillStyle = '#0f172a';
            ctx.fillRect(trenchX - 6, trenchY, 12, 35);

            // Rotor da Turbina
            ctx.translate(trenchX, trenchY);
            ctx.rotate(simState.turbineAngle);

            const blades = 3;
            for (let i = 0; i < blades; i++) {
                ctx.rotate((Math.PI * 2) / blades);
                ctx.beginPath();
                ctx.ellipse(0, -18, 5, 18, 0, 0, Math.PI * 2);
                ctx.fillStyle = simState.hydroActive ? 'rgba(0, 242, 254, 0.8)' : '#475569';
                ctx.shadowColor = '#00f2fe';
                ctx.shadowBlur = simState.hydroActive ? 10 : 0;
                ctx.fill();
            }

            ctx.restore();
        }

        function drawSurfaceGridAndEnergyBeams() {
            const w = canvas.width;
            const h = canvas.height;

            ctx.save();
            const cityX = w * 0.45;
            const cityY = h * 0.15;

            // Silhueta Futurista de Edifícios/Hub de Superfície
            ctx.fillStyle = '#09101d';
            ctx.fillRect(cityX - 50, cityY - 25, 20, 25);
            ctx.fillRect(cityX - 25, cityY - 45, 18, 45);
            ctx.fillRect(cityX, cityY - 60, 28, 60); // Torre Principal Receptora
            ctx.fillRect(cityX + 32, cityY - 30, 22, 30);

            // Janelas e luzes da cidade
            ctx.fillStyle = '#38ef7d';
            ctx.shadowColor = '#38ef7d';
            ctx.shadowBlur = 6;
            ctx.fillRect(cityX + 10, cityY - 50, 8, 4);
            ctx.fillRect(cityX + 10, cityY - 38, 8, 4);
            ctx.fillRect(cityX - 20, cityY - 30, 6, 3);

            // Receptor de Energia (Domo Iluminado)
            ctx.beginPath();
            ctx.arc(cityX + 14, cityY - 60, 8, Math.PI, 0);
            ctx.fillStyle = '#38ef7d';
            ctx.fill();

            // Desenhar Linhas de Condução e Transmissão de Energia até o Hub
            if (Math.random() < 0.3) {
                if (simState.magmaActive) triggerEnergyPulse('magma');
                if (simState.seismicActive && Math.random() < 0.2) triggerEnergyPulse('seismic');
                if (simState.hydroActive) triggerEnergyPulse('hydro');
            }

            // Atualizar e renderizar pulsos de energia em trânsito
            for (let i = simState.energyPulses.length - 1; i >= 0; i--) {
                const pulse = simState.energyPulses[i];
                pulse.progress += 0.025;

                pulse.x = pulse.startX + (pulse.endX - pulse.startX) * pulse.progress;
                pulse.y = pulse.startY + (pulse.endY - pulse.startY) * pulse.progress;

                ctx.beginPath();
                ctx.arc(pulse.x, pulse.y, 3.5, 0, Math.PI * 2);
                ctx.fillStyle = pulse.color;
                ctx.shadowColor = pulse.color;
                ctx.shadowBlur = 10;
                ctx.fill();

                if (pulse.progress >= 1) {
                    simState.energyPulses.splice(i, 1);
                }
            }

            ctx.restore();
        }

        function updatePhysicsAndMetrics() {
            simState.time += 1;

            if (simState.magmaPulseTimer > 0) {
                simState.magmaPulseTimer--;
            }

            // 1. Cálculo da Energia Magmática (MHD)
            const baseTemp = simState.magmaTemp + (simState.magmaPulseTimer > 0 ? 2000 : 0);
            simState.magmaPower = simState.magmaActive 
                ? (baseTemp / 3800) * 10.25 
                : 0;

            // 2. Cálculo da Energia Cinética Sísmica
            const stressRatio = simState.seismicStress / 100;
            simState.seismicPower = simState.seismicActive 
                ? (stressRatio * 5.8) + (simState.seismicWaves.length * 1.2) 
                : 0;

            // 3. Cálculo da Energia Hidrocinética Oceânica (P ~ v^3)
            simState.hydroPower = simState.hydroActive 
                ? Math.pow(simState.oceanSpeed / 4.2, 3) * 3.99 
                : 0;

            // 4. Totais e Eficiência
            simState.totalPower = simState.magmaPower + simState.seismicPower + simState.hydroPower;
            simState.stabilizationPct = Math.min(99.9, 100 - (simState.seismicStress * (simState.seismicActive ? 0.15 : 0.8)));
            simState.efficiencyPct = 91.0 + (simState.magmaTemp / 6000) * 6.5;

            // Atualização da DOM
            document.getElementById('valTotalPower').innerText = `${simState.totalPower.toFixed(2)} TW`;
            document.getElementById('valMagmaPower').innerText = `${simState.magmaPower.toFixed(2)} TW`;
            document.getElementById('valMagmaTemp').innerText = `${baseTemp} °C`;

            document.getElementById('valSeismicPower').innerText = `${simState.seismicPower.toFixed(2)} TW`;
            document.getElementById('valSeismicMag').innerText = `Estresse ${simState.seismicStress}%`;

            document.getElementById('valHydroPower').innerText = `${simState.hydroPower.toFixed(2)} TW`;
            document.getElementById('valOceanSpeed').innerText = `${simState.oceanSpeed.toFixed(1)} m/s`;

            document.getElementById('valStabilization').innerText = `${simState.stabilizationPct.toFixed(1)} %`;
            document.getElementById('valEfficiency').innerText = `${simState.efficiencyPct.toFixed(1)} %`;
        }

        function animate() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Camadas do Cenário 2D
            drawGeologicalCrossSection();
            drawMagmaChamber();
            drawGeothermalProbes();
            drawSeismicSystem();
            drawBenthicHydroSystem();
            drawSurfaceGridAndEnergyBeams();

            // Atualização de Estados
            updatePhysicsAndMetrics();

            requestAnimationFrame(animate);
        }

        // Executar inicializações
        initParticles();
        animate();
    </script>
</body>
</html>
