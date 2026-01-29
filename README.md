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
            --sidebar-bg: #161617;
        }

        * { box-sizing: border-box; margin: 0; padding: 0; font-family: "SF Pro Display", -apple-system, sans-serif; user-select: none; }

        body {
            background: url('https://www.apple.com/v/ipad-pro/aj/images/overview/experience/specs__e6z9m7w7x9ua_large.jpg') no-repeat center center fixed;
            background-size: cover;
            height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            overflow: hidden;
            padding-top: 2vh;
        }

        /* --- MAIN UI --- */
        .main-card {
            background-color: var(--card-bg);
            width: 80%;
            max-width: 850px;
            padding: 60px 0;
            border-radius: 30px;
            text-align: center;
            box-shadow: 0 30px 60px rgba(0,0,0,0.6);
            z-index: 10;
        }

        h1 { font-size: 90px; font-weight: 600; margin-bottom: 40px; letter-spacing: -2px; color: white; }

        .btn {
            width: 280px; padding: 16px; border-radius: 40px; border: none;
            font-size: 17px; cursor: pointer; margin-bottom: 12px; font-weight: 500;
        }
        .btn-blue { background: var(--apple-blue); color: white; }
        .btn-dark { background: #2c2c2e; color: white; }

        .glass-panel {
            background: rgba(20, 20, 22, 0.7);
            backdrop-filter: blur(40px) saturate(180%);
            -webkit-backdrop-filter: blur(40px) saturate(180%);
            width: 90%;
            max-width: 1000px;
            height: 450px;
            margin-top: -60px;
            border-radius: 40px;
            padding: 40px;
            border: 0.5px solid rgba(255,255,255,0.1);
        }

        .grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(160px, 1fr));
            gap: 40px;
            overflow-y: auto;
        }

        .item { text-align: center; color: white; }
        .thumbnail {
            width: 150px; aspect-ratio: 16/10;
            background: #333; border-radius: 4px;
            margin: 0 auto 10px; box-shadow: 0 4px 10px rgba(0,0,0,0.5);
        }

        /* --- EDITOR (IMG_1288 Style) --- */
        #editor {
            position: fixed; inset: 0;
            background: #000; z-index: 2000;
            display: none; flex-direction: column;
        }

        .editor-top-bar {
            height: 55px; background: #1c1c1e;
            display: flex; justify-content: space-between; align-items: center;
            padding: 0 20px; border-bottom: 0.5px solid #333;
        }

        .editor-main { display: flex; flex-grow: 1; }

        .sidebar {
            width: 180px; background: var(--sidebar-bg);
            border-right: 0.5px solid #333; padding: 20px 10px;
            display: flex; flex-direction: column; gap: 15px;
        }

        .slide-thumb {
            width: 100%; aspect-ratio: 16/10;
            background: white; border-radius: 4px;
            border: 2px solid var(--apple-blue);
        }

        .canvas-area {
            flex-grow: 1; background: #111;
            display: flex; justify-content: center; align-items: center;
            padding: 40px;
        }

        .slide-white {
            width: 90%; max-width: 800px; aspect-ratio: 16/9;
            background: white; color: black; padding: 60px;
            box-shadow: 0 0 30px rgba(0,0,0,0.5);
        }

        /* Context Menu */
        #contextMenu {
            position: fixed; background: rgba(35, 35, 35, 0.95);
            border-radius: 12px; width: 200px; display: none; z-index: 3000;
            border: 0.5px solid #444; overflow: hidden;
        }
        .menu-item { padding: 12px 20px; color: white; border-bottom: 0.5px solid #444; }
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
        <div class="editor-top-bar">
            <div style="color: var(--apple-blue); cursor: pointer;" onclick="closeEditor()">❮ Präsentationen</div>
            <div id="editorTitle">Präsentation 3</div>
            <div style="color: var(--apple-blue);">Teilen</div>
        </div>
        <div class="editor-main">
            <div class="sidebar">
                <div class="slide-thumb"></div>
                <div style="width: 30px; height: 30px; background: #333; border-radius: 5px; margin-top: auto; display: flex; align-items: center; justify-content: center;">+</div>
            </div>
            <div class="canvas-area">
                <div class="slide-white">
                    <h1 id="canvasTitle" style="color: black; font-size: 48px; text-align: left;">Titel der Präsentation</h1>
                    <p style="color: #666; font-size: 24px;">Präsentationsuntertitel</p>
                </div>
            </div>
        </div>
    </div>

    <div id="contextMenu">
        <div class="menu-item" onclick="renameCurrent()">Umbenennen</div>
        <div class="menu-item" style="color: #ff453a;" onclick="deleteCurrent()">Löschen</div>
    </div>

    <script>
        // ... (Gleiche Logik wie vorher, aber angepasst an das neue UI)
        const grid = document.getElementById('grid');
        const menu = document.getElementById('contextMenu');
        const editor = document.getElementById('editor');
        let selectedId = null;
        let pressTimer = null;

        function addPresentation() {
            const name = prompt("Name:");
            if (!name) return;
            const p = { name, date: "Heute, 14:30", id: Date.now() };
            saveToStorage(p);
            renderItem(p);
        }

        function renderItem(p) {
            const div = document.createElement('div');
            div.className = 'item';
            div.id = 'p-' + p.id;
            div.onmousedown = (e) => { selectedId = p.id; pressTimer = setTimeout(() => showMenu(e), 600); };
            div.onmouseup = () => clearTimeout(pressTimer);
            div.onclick = () => { if(menu.style.display !== 'block') openEditor(p.name); };
            div.innerHTML = `<div class="thumbnail"></div><p>${p.name}</p><span>${p.date}</span>`;
            grid.appendChild(div);
        }

        function showMenu(e) {
            menu.style.display = 'block';
            menu.style.left = e.pageX + 'px';
            menu.style.top = e.pageY + 'px';
        }

        function openEditor(name) {
            document.getElementById('editorTitle').innerText = name;
            document.getElementById('canvasTitle').innerText = name;
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

        window.onclick = () => menu.style.display = 'none';
        document.addEventListener('DOMContentLoaded', loadPresentations);

        function deleteCurrent() {
            document.getElementById('p-' + selectedId).remove();
            let list = JSON.parse(localStorage.getItem('myKeynotes')).filter(p => p.id !== selectedId);
            localStorage.setItem('myKeynotes', JSON.stringify(list));
        }
    </script>
</body>
</html>
