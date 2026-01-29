<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Keynote Web App</title>
    <style>
        :root {
            --apple-blue: #007aff;
            --card-bg: #1c1c1e;
        }

        * { box-sizing: border-box; margin: 0; padding: 0; font-family: -apple-system, sans-serif; }

        body {
            background: radial-gradient(circle at top, #3b82f6 0%, #1d4ed8 100%);
            height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            color: white;
            padding-top: 5vh;
        }

        .main-card {
            background-color: var(--card-bg);
            width: 90%;
            max-width: 800px;
            padding: 60px 20px;
            border-radius: 35px;
            text-align: center;
            box-shadow: 0 20px 50px rgba(0,0,0,0.4);
            z-index: 10;
        }

        h1 { font-size: 72px; margin-bottom: 40px; font-weight: 600; }

        .btn {
            width: 280px;
            padding: 16px;
            border-radius: 40px;
            border: none;
            font-size: 17px;
            cursor: pointer;
            margin-bottom: 12px;
            display: block;
            margin-left: auto;
            margin-right: auto;
            transition: opacity 0.2s;
        }

        .btn-blue { background: var(--apple-blue); color: white; }
        .btn-dark { background: #2c2c2e; color: white; }

        .glass-panel {
            background: rgba(28, 28, 30, 0.6);
            backdrop-filter: blur(40px) saturate(180%);
            -webkit-backdrop-filter: blur(40px) saturate(180%);
            width: 94%;
            max-width: 900px;
            flex-grow: 1;
            margin-top: -50px;
            margin-bottom: 20px;
            border-radius: 35px;
            padding: 40px;
            border: 0.5px solid rgba(255,255,255,0.1);
            overflow-y: auto;
        }

        .grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(140px, 1fr));
            gap: 30px;
        }

        .item { text-align: center; animation: fadeIn 0.4s ease; }
        
        .thumbnail {
            width: 100%;
            aspect-ratio: 16/10;
            background: #2c2c2e;
            border-radius: 8px;
            margin-bottom: 8px;
            border: 1px solid rgba(255,255,255,0.1);
            cursor: pointer;
        }

        .item p { font-size: 14px; font-weight: 500; }
        .item span { font-size: 11px; color: #8e8e93; }

        @keyframes fadeIn { from { opacity: 0; transform: scale(0.9); } to { opacity: 1; transform: scale(1); } }
    </style>
</head>
<body>

    <div class="main-card">
        <h1>Keynote</h1>
        <button class="btn btn-blue" onclick="addPresentation()">Thema auswählen</button>
        <button class="btn btn-dark" onclick="alert('Gliederungsmodus aktiv')">Gliederung starten</button>
    </div>

    <div class="glass-panel">
        <div id="grid" class="grid">
            </div>
    </div>

    <script>
        const grid = document.getElementById('grid');

        // Lade vorhandene Daten
        document.addEventListener('DOMContentLoaded', loadPresentations);

        function addPresentation() {
            const name = prompt("Name der Präsentation:", "Neue Präsentation");
            if (!name) return;

            const date = new Date().toLocaleString('de-DE', { 
                day: '2-digit', month: '2-digit', year: '2-digit', hour: '2-digit', minute: '2-digit' 
            });

            const presentation = { name, date, id: Date.now() };
            saveToStorage(presentation);
            renderItem(presentation);
        }

        function renderItem(p) {
            const div = document.createElement('div');
            div.className = 'item';
            div.innerHTML = `
                <div class="thumbnail" onclick="alert('Öffne: ${p.name}')"></div>
                <p>${p.name}</p>
                <span>${p.date}</span>
            `;
            grid.appendChild(div);
        }

        function saveToStorage(p) {
            let list = JSON.parse(localStorage.getItem('myKeynotes') || '[]');
            list.push(p);
            localStorage.setItem('myKeynotes', JSON.stringify(list));
        }

        function loadPresentations() {
            let list = JSON.parse(localStorage.getItem('myKeynotes') || '[]');
            list.forEach(renderItem);
        }
    </script>
</body>
</html>
