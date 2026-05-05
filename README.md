<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Pro Speed Tracker</title>
    <style>
        :root {
            --neon-green: #00ff99;
            --alert-red: #ff3333;
        }
        body {
            background-color: #000;
            color: var(--neon-green);
            font-family: 'Arial', sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
            overflow: hidden;
            transition: transform 0.3s ease;
        }
        /* HUD Mode: Flips the whole screen */
        .hud-mode {
            transform: scaleX(-1);
        }
        #speed-display {
            font-size: 120px;
            font-weight: 800;
            margin: 0;
            text-shadow: 0 0 20px var(--neon-green);
        }
        .unit { font-size: 24px; color: #fff; margin-bottom: 20px; }
        
        .stats-container {
            display: flex;
            gap: 40px;
            margin-top: 30px;
            text-align: center;
        }
        .stat-label { font-size: 12px; color: #888; display: block; }
        .stat-value { font-size: 24px; color: #fff; font-weight: bold; }

        .controls {
            margin-top: 50px;
            display: flex;
            gap: 10px;
        }
        button {
            background: transparent;
            border: 2px solid var(--neon-green);
            color: var(--neon-green);
            padding: 10px 20px;
            border-radius: 5px;
            cursor: pointer;
            font-weight: bold;
        }
        button:active { background: var(--neon-green); color: #000; }
        .alert { color: var(--alert-red) !important; text-shadow: 0 0 20px var(--alert-red) !important; }
    </style>
</head>
<body>

    <div id="speed-display">0</div>
    <div class="unit" id="unit-text">KM/H</div>

    <div class="stats-container">
        <div>
            <span class="stat-label">TOP SPEED</span>
            <span class="stat-value" id="top-speed">0.0</span>
        </div>
        <div>
            <span class="stat-label">AVG SPEED</span>
            <span class="stat-value" id="avg-speed">0.0</span>
        </div>
    </div>

    <div class="controls">
        <button onclick="toggleHud()">HUD MODE</button>
        <button onclick="toggleUnits()">SWITCH UNIT</button>
    </div>

    <script>
        let topSpeed = 0;
        let totalSpeed = 0;
        let count = 0;
        let isKmh = true;
        const speedEl = document.getElementById('speed-display');
        const topSpeedEl = document.getElementById('top-speed');
        const avgSpeedEl = document.getElementById('avg-speed');
        const unitTextEl = document.getElementById('unit-text');

        function toggleHud() {
            document.body.classList.toggle('hud-mode');
        }

        function toggleUnits() {
            isKmh = !isKmh;
            unitTextEl.innerText = isKmh ? "KM/H" : "MPH";
        }

        if ("geolocation" in navigator) {
            navigator.geolocation.watchPosition((pos) => {
                let speedMS = pos.coords.speed || 0;
                // Convert m/s to chosen unit
                let speed = isKmh ? (speedMS * 3.6) : (speedMS * 2.23694);
                
                if (speed < 0) speed = 0; // Handle noise

                // Update UI
                speedEl.innerText = speed.toFixed(0);
                
                // Track Top Speed
                if (speed > topSpeed) {
                    topSpeed = speed;
                    topSpeedEl.innerText = topSpeed.toFixed(1);
                }

                // Calculate Average
                if (speed > 0) {
                    totalSpeed += speed;
                    count++;
                    avgSpeedEl.innerText = (totalSpeed / count).toFixed(1);
                }

                // Speed Alert (Pro Feature)
                if (isKmh && speed > 120) {
                    speedEl.classList.add('alert');
                } else {
                    speedEl.classList.remove('alert');
                }

            }, (err) => console.error(err), { enableHighAccuracy: true });
        }
    </script>
</body>
</html>