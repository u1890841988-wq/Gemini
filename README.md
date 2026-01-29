<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Keynote iPad Pro</title>
    <style>
        :root {
            --apple-blue: #007aff;
            --card-bg: #1c1c1e;
            --editor-bg: #1e1e1e;
        }

        * { box-sizing: border-box; margin: 0; padding: 0; font-family: -apple-system, sans-serif; user-select: none; }

        body {
            background: radial-gradient(circle at 50% 0%, #3b82f6 0%, #1e3a8a 100%);
            height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            color: white;
            padding-top: 40px;
            overflow: hidden;
        }

        /* --- MAIN GUI --- */
        .main-card {
            background-color: var(--card-bg);
            width: 85%;
            max-width: 800px;
            height: 380px;
            border-radius: 38px;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            box-shadow: 0 30px 60px rgba(0,0,0,0.5);
            z-index: 10;
        }

        h1 { font-size: 82px; font-weight: 600; margin-bottom: 45px; letter-spacing: -1.5px; }

        .btn {
            width: 280px; padding: 16px; border-radius: 40px; border: none;
            font-size: 17px; cursor: pointer; margin-bottom: 12px;
            font-weight: 500;
        }
        .btn-blue { background: var(--apple-blue); color: white; }
        .btn-dark { background: #2c2c2e; color: white; }

        .glass-panel {
            background: rgba(20, 20, 22, 0.55);
            backdrop-filter: blur(50px) saturate(200%);
            -webkit-backdrop-filter: blur(50px) saturate(200%);
            width: 92%;
            max-width: 950px;
            height: 480px;
            margin-top: -60px;
            border-radius: 45px;
            padding: 50px 30px 30px 30px;
            border: 1px solid rgba(255,255,255,0.08);
        }

        .grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
            gap: 30px;
            overflow-y: auto;
            max-height: 100%;
        }

        .item { text-align: center; cursor: pointer; }
        .thumbnail {
            width: 140px; aspect-ratio: 16/10;
            background: #2c2c2e; border-radius: 8px;
            margin: 0 auto 10px; border: 0.5px solid rgba(255,255,255,0.1);
            box-shadow: 0 4px 15px rgba(0,0,0,0.3);
        }

        /* --- EDITOR GUI --- */
        #editor {
            position: fixed; inset: 0;
            background: #000; z-index: 2000;
            display: none; flex-direction: column;
        }

        .editor-toolbar {
            height: 50px;
            background: #1c1c1e;
            display: flex;
            justify-content: space-between;
            align-items: center;
            padding: 0 20px;
            border-bottom: 0.5px solid #333;
        }

        .toolbar-btn { color: var(--apple-blue); font-size: 17px; cursor: pointer; }

        .editor-content {
            flex-grow: 1;
            display: flex;
            justify-content: center;
            align-items: center;
            background: #111;
            padding: 40px;
        }

        .slide-canvas {
            width: 80%;
            aspect-ratio: 16/9;
            background: white;
            box-shadow: 0 0 40px rgba(0,0,0,0.5);
            display: flex;
            justify-content: center;
            align-items: center;
            color: #333;
        }

        /* Kontext Menü */
        #contextMenu {
            position: fixed; background: rgba(30, 30, 30, 0.9);
            backdrop-filter: blur(20px); border-radius: 14px;
            width: 190px; display: none; z-index: 3000;
            border: 0.5px solid rgba(255,255,255,0.15);
            box-shadow: 0 10px 40px rgba(0,0,0,0.4);
        }
        .menu-item {
            padding: 12px 18px; font-size: 16px; 
            border-bottom: 0.5px solid rgba(255,255,255,0.1);
            display: flex; justify-content: space-between;
        }
    </style>
</head>
<body>

    <div class="main-card">
        <h1>Keynote</h1>
        <button class="btn btn-blue" onclick="addPresentation()">Thema auswählen</button>
        <button class="btn btn-dark">Gliederung starten</button>
    </div>

    <div class="glass-panel">
        <div id="grid" class="grid"></div>
    </div>

    <div id="editor">
        <div class="editor-toolbar">
            <div class="toolbar-btn" onclick="closeEditor()">❮ Präsentationen</div>
            <div id="editorTitle" style="font-weight: 600;">Titel</div>
            <div class="toolbar-btn" style="color: #aaa;">Bearbeiten</div>
        </div>
        <div class="editor-content">
            <div class="slide-canvas">
                <h2 style="font-size: 40px;" id="canvasText">Präsentation</h2>
            </div>
        </div>
    </div>

    <div id="contextMenu">
        <div class="menu-item" onclick="renameCurrent()">Umbenennen</div>
        <div class="menu-item" style="color: #ff453a;" onclick="deleteCurrent()">Löschen</div>
    </div>

    <script>
        const grid = document.getElementById('grid');
        const menu = document.getElementById('contextMenu');
        const editor = document.getElementById('editor');
        let selectedId = null;
        let pressTimer = null;

        document.addEventListener('DOMContentLoaded', loadPresentations);
        window.onclick = () => menu.style.display = 'none';

        function addPresentation() {
            const name = prompt("Name der Präsentation:", "Unbenannt");
            if (!name) return;
            const p = { name, date: new Date().toLocaleDateString(), id: Date.now() };
            saveToStorage(p);
            renderItem(p);
        }

        function renderItem(p) {
            const div = document.createElement('div');
            div.className = 'item';
            div.id = 'p-' + p.id;
            
            div.onmousedown = (e) => startPress(e, p.id);
            div.ontouchstart = (e) => startPress(e, p.id);
            div.onmouseup = () => clearTimeout(pressTimer);
            div.ontouchend = () => clearTimeout(pressTimer);
            div.onclick = () => openPresentation(p.name);

            div.innerHTML = `
                <div class="thumbnail"></div>
                <p id="title-${p.id}" style="font-size:14px;">${p.name}</p>
                <span style="font-size:11px; color:#888;">${p.date}</span>
            `;
            grid.appendChild(div);
        }

        function startPress(e, id) {
            selectedId = id;
            pressTimer = setTimeout(() => {
                showMenu(e);
            }, 600);
        }

        function showMenu(e) {
            e.preventDefault();
            const x = e.pageX || (e.touches ? e.touches[0].pageX : 0);
            const y = e.pageY || (e.touches ? e.touches[0].pageY : 0);
            menu.style.display = 'block';
            menu.style.left = x + 'px';
            menu.style.top = y + 'px';
        }

        function openPresentation(name) {
            if (menu.style.display === 'block') return;
            document.getElementById('editorTitle').innerText = name;
            document.getElementById('canvasText').innerText = name;
            editor.style.display = 'flex';
        }

        function closeEditor() { editor.style.display = 'none'; }

        function saveToStorage(p) {
            let list = JSON.parse(localStorage.getItem('myKeynotes') || '[]');
            list.push(p);
            localStorage.setItem('myKeynotes', JSON.stringify(list));
        }

        function loadPresentations() {
            let list = JSON.parse(localStorage.getItem('myKeynotes') || '[]');
            list.forEach(renderItem);
        }

        function deleteCurrent() {
            let list = JSON.parse(localStorage.getItem('myKeynotes') || '[]');
            list = list.filter(p => p.id !== selectedId);
            localStorage.setItem('myKeynotes', JSON.stringify(list));
            document.getElementById('p-' + selectedId).remove();
        }

        function renameCurrent() {
            const newName = prompt("Neuer Name:");
            if (!newName) return;
            let list = JSON.parse(localStorage.getItem('myKeynotes') || '[]');
            const p = list.find(x => x.id === selectedId);
            if (p) {
                p.name = newName;
                localStorage.setItem('myKeynotes', JSON.stringify(list));
                document.getElementById('title-' + selectedId).innerText = newName;
            }
        }
    </script>
</body>
</html>
