<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Keynote Style Dashboard</title>
    <style>
        * { box-sizing: border-box; margin: 0; padding: 0; }

        body {
            /* Der blaue Wellen-Hintergrund */
            background: linear-gradient(135deg, #1e40af, #3b82f6, #60a5fa);
            height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
            color: white;
            overflow: hidden;
        }

        /* Obere schwarze Box */
        .hero-card {
            background: rgba(30, 30, 30, 0.95);
            width: 90%;
            max-width: 850px;
            height: 380px;
            border-radius: 40px 40px 0 0;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            text-align: center;
            box-shadow: 0 10px 30px rgba(0,0,0,0.3);
            z-index: 2;
        }

        .hero-card h1 {
            font-size: 80px;
            font-weight: 700;
            margin-bottom: 40px;
            letter-spacing: -2px;
        }

        .btn {
            width: 280px;
            padding: 14px;
            border-radius: 25px;
            border: none;
            font-size: 16px;
            font-weight: 500;
            cursor: pointer;
            margin-bottom: 12px;
            transition: opacity 0.2s;
        }

        .btn-blue { background: #007aff; color: white; }
        .btn-dark { background: #2c2c2e; color: white; }
        .btn:hover { opacity: 0.8; }

        /* Unterer Glas-Bereich */
        .content-area {
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(25px); /* Der entscheidende Blur-Effekt */
            -webkit-backdrop-filter: blur(25px);
            width: 95%;
            max-width: 950px;
            height: 450px;
            margin-top: -30px;
            border-radius: 40px;
            padding: 30px;
            box-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.2);
            border: 1px solid rgba(255, 255, 255, 0.1);
        }

        /* Navigation oben im Glas-Bereich */
        .nav-bar {
            display: flex;
            justify-content: center;
            gap: 20px;
            margin-bottom: 40px;
        }

        .nav-item {
            padding: 6px 16px;
            border-radius: 10px;
            font-size: 14px;
            cursor: pointer;
        }

        .nav-active { background: rgba(255, 255, 255, 0.2); }

        /* Raster für die Präsentationen */
        .grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(140px, 1fr));
            gap: 30px;
            padding: 0 40px;
        }

        .item {
            text-align: center;
            transition: transform 0.2s;
            cursor: pointer;
        }

        .item:hover { transform: scale(1.05); }

        .thumb {
            width: 130px;
            height: 85px;
            background: white;
            border-radius: 4px;
            margin: 0 auto 10px;
            box-shadow: 0 4px 10px rgba(0,0,0,0.2);
        }

        .item p { font-size: 13px; font-weight: 500; margin-bottom: 2px; }
        .item span { font-size: 11px; color: rgba(255,255,255,0.6); }

    </style>
</head>
<body>

    <div class="hero-card">
        <h1>Keynote</h1>
        <button class="btn btn-blue">Thema auswählen</button>
        <button class="btn btn-dark">Gliederung starten</button>
    </div>

    <div class="content-area">
        <div class="nav-bar">
            <div class="nav-item">Verlauf</div>
            <div class="nav-item nav-active">Geteilt</div>
            <div class="nav-item">Durchsuchen</div>
        </div>

        <div class="grid">
            <div class="item">
                <div class="thumb" style="background: #333;"></div>
                <p>Präsentation 4</p>
                <span>Heute, 14:13</span>
            </div>
            <div class="item">
                <div class="thumb"></div>
                <p>Präsentation 3</p>
                <span>18.11.25, 11:13</span>
            </div>
            <div class="item">
                <div class="thumb" style="background: linear-gradient(to right, #4facfe, #00f2fe);"></div>
                <p>Urlaub</p>
                <span>16.06.25, 10:26</span>
            </div>
        </div>
    </div>

</body>
</html>
