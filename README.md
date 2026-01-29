<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Keynote iPad Pro</title>
    <style>
        :root {
            --apple-blue: #007aff;
            --glass-bg: rgba(28, 28, 30, 0.7);
            --card-bg: #1c1c1e;
        }

        * { box-sizing: border-box; margin: 0; padding: 0; -webkit-tap-highlight-color: transparent; }

        body {
            background: radial-gradient(circle at top, #3b82f6 0%, #1d4ed8 50%, #1e3a8a 100%);
            height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            font-family: -apple-system, BlinkMacSystemFont, "SF Pro Display", "Helvetica Neue", sans-serif;
            color: white;
            overflow: hidden;
            padding-top: 40px;
        }

        /* Obere Keynote Card */
        .main-card {
            background-color: var(--card-bg);
            width: 90%;
            max-width: 800px;
            height: 420px;
            border-radius: 30px;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            box-shadow: 0 20px 50px rgba(0,0,0,0.4);
            z-index: 10;
        }

        h1 {
            font-size: 84px;
            font-weight: 600;
            letter-spacing: -1px;
            margin-bottom: 50px;
        }

        .btn {
            width: 260px;
            padding: 16px;
            border-radius: 40px; /* Perfekt rund gelutscht */
            border: none;
            font-size: 17px;
            font-weight: 400;
            cursor: pointer;
            margin-bottom: 12px;
            transition: background 0.2s;
        }

        .btn-blue { background: var(--apple-blue); color: white; }
        .btn-dark { background: #2c2c2e; color: white; }

        /* Unterer Glas-Bereich (Dashboard) */
        .glass-panel {
            background: rgba(30, 30, 30, 0.6);
            backdrop-filter: blur(40px) saturate(180%);
            -webkit-backdrop-filter: blur(40px) saturate(180%);
            width: 94%;
            max-width: 920px;
            height: 400px;
            margin-top: -60px; /* Schiebt es unter die obere Karte */
            border-radius: 35px;
            padding: 20px;
            border: 0.5px solid rgba(255,255,255,0.1);
        }

        /* Header im Glas-Panel */
        .panel-header {
            display: flex;
            justify-content: center;
            align-items: center;
            position: relative;
            margin-bottom: 30px;
        }

        .tab-switcher {
            display: flex;
            background: rgba(255, 255, 255, 0.1);
            padding: 3px;
            border-radius: 10px;
        }

        .tab {
            padding: 6px 16px;
            font-size: 14px;
            border-radius: 8px;
            cursor: pointer;
        }

        .tab.active { background: rgba(255, 255, 255, 0.15); color: #60a5fa; }

        /* Grid für die Präsentationen */
        .grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(130px, 1fr));
            gap: 25px;
            padding: 0 20px;
        }

        .item { text-align: center; }

        .thumbnail {
            width: 130px;
            aspect-ratio: 16/10;
            background: #2c2c2e;
            border-radius: 6px;
            margin: 0 auto 10px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.3);
            border: 0.5px solid rgba(255,255,255,0.1);
        }

        .item p { font-size: 13px; font-weight: 500; }
        .item span { font-size: 11px; color: #8e8e93; display: block; margin-top: 2px; }

    </style>
</head>
<body>

    <div class="main-card">
        <h1>Keynote</h1>
        <button class="btn btn-blue">Thema auswählen</button>
        <button class="btn btn-dark">Gliederung starten</button>
    </div>

    <div class="glass-panel">
        <div class="panel-header">
            <div class="tab-switcher">
                <div class="tab active">Verlauf</div>
                <div class="tab">Geteilt</div>
                <div class="tab">Durchsuchen</div>
            </div>
        </div>

        <div class="grid">
            <div class="item">
                <div class="thumbnail" style="background: linear-gradient(45deg, #444, #222);"></div>
                <p>Präsentation 4</p>
                <span>Heute, 14:13</span>
                <span>4,8 MB</span>
            </div>
            <div class="item">
                <div class="thumbnail" style="background: white;"></div>
                <p>Präsentation 3</p>
                <span>18.11.25, 11:13</span>
                <span>409 KB</span>
            </div>
            <div class="item">
                <div class="thumbnail" style="background: #fff; display:flex; align-items:center; justify-content:center; color:#333; font-size:8px;">Völkerrecht</div>
                <p>Präsentation</p>
                <span>11.11.25, 12:30</span>
                <span>55,3 MB</span>
            </div>
        </div>
    </div>

</body>
</html>
