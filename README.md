<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Keynote Pro</title>
    <style>
        :root {
            --apple-blue: #007aff;
            --card-bg: #1c1c1e;
        }

        * { box-sizing: border-box; margin: 0; padding: 0; font-family: -apple-system, sans-serif; user-select: none; }

        body {
            background: radial-gradient(circle at top, #3b82f6 0%, #1d4ed8 100%);
            height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            color: white;
            padding-top: 5vh;
            overflow: hidden;
        }

        /* Haupt-Interface */
        .main-card {
            background-color: var(--card-bg);
            width: 90%; max-width: 800px;
            padding: 50px 20px;
            border-radius: 35px;
            text-align: center;
            box-shadow: 0 20px 50px rgba(0,0,0,0.4);
            z-index: 10;
        }

        h1 { font-size: 72px; margin-bottom: 30px; font-weight: 600; }

        .btn {
            width: 260px; padding: 16px; border-radius: 40px; border: none;
            font-size: 17px; cursor: pointer; margin-bottom: 12px;
            display: block; margin-left: auto; margin-right: auto;
        }
        .btn-blue { background: var(--apple-blue); color: white; }
        .btn-dark { background: #2c2c2e; color: white; }

        .glass-panel {
            background: rgba(28, 28, 30, 0.6);
            backdrop-filter: blur(40px) saturate(180%);
            -webkit-backdrop-filter: blur(40px) saturate(180%);
            width: 94%; max-width: 900px;
            flex-grow: 1; margin-top: -50px; margin-bottom: 20px;
            border-radius: 35px; padding: 40px;
            border: 0.5px solid rgba(255,255,255,0.1);
            overflow-y: auto;
        }

        .grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(140px, 1fr)); gap: 30px; }

        /* Präsentations-Item */
        .item { text-align: center; position: relative; cursor: pointer; }
        
        .thumbnail {
            width: 100%; aspect-ratio: 16/10;
            background: #2c2c2e; border-radius: 8px;
            margin-bottom: 8px; border: 1px solid rgba(255,255,255,0.1);
            transition: transform 0.2s;
        }
        .item:active .thumbnail { transform: scale(0.95); }

        .item p { font-size: 14px; font-weight: 500; overflow: hidden; text-overflow: ellipsis; white-space: nowrap; }
        .item span { font-size: 11px; color: #8e8e93; }

        /* Kontextmenü (Hidden by default) */
        #contextMenu {
            position: fixed; background: rgba(44, 44, 46, 0.95);
            backdrop-filter: blur(20px); border-radius: 12px;
            width: 180px; display: none; z-index: 1000;
            box-shadow: 0 10px 30px rgba(0,0,0,0.5);
            border: 0.5px solid rgba(255,255,255,0.2);
        }

        .menu-item {
            padding: 12px 16px; font-size: 16px; cursor: pointer;
            border-bottom: 0.5px solid rgba(255,255,255,0.1);
            display: flex; justify-content: space-between;
        }
        .menu-item:last-child { border-bottom: none; color: #ff453a; }
        .menu-item:active { background: rgba(255,255,255,0.1); }

        /* Editor Overlay */
        #editor {
            position: fixed; top:0; left:0; width: 100%; height: 100%;
            background: #000; z-index: 2000; display: none;
            flex-direction: column; align-items: center; justify-content: center;
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

    <div id="contextMenu">
        <div class="menu-item" onclick="renameCurrent()">Umbenennen</div>
        <div class="menu-item" onclick="deleteCurrent()">Löschen</div>
    </div>

    <div id="editor">
        <h2 id="editorTitle">Präsentation</h2>
        <p style="color: #888; margin-top: 20px;">[Editor Modus - Hier könnte dein Inhalt stehen]</p>
        <button class="btn btn-blue" style="margin-top: 50px;" onclick="closeEditor()">Schließen</button>
    </div>

    <script>
        const grid = document.getElementById('grid');
        const menu = document.getElementById('contextMenu');
        const editor = document.getElementById('editor');
        let selectedId = null;
        let pressTimer = null;

        document.addEventListener('DOMContentLoaded', loadPresentations);
        
        // Schließt Menü bei Klick irgendwo anders
        window.onclick = () => menu.style.display = 'none';

        function addPresentation() {
            const name = prompt("Name der Präsentation:", "Neue Präsentation");
            if (!name) return;
            const p = { name, date: new Date().toLocaleDateString(), id: Date.now() };
            saveToStorage(p);
            renderItem(p);
        }

        function renderItem(p) {
            const div = document.createElement('div');
            div.className = 'item';
            div.id = 'p-' + p.id;
            
            // Long Press Logik
            div.onmousedown = (e) => startPress(e, p.id);
            div.ontouchstart = (e) => startPress(e, p.id);
            div.onmouseup = () => clearTimeout(pressTimer);
            div.ontouchend = () => clearTimeout(pressTimer);
            
            // Normaler Klick zum Öffnen
            div.onclick = () => openPresentation(p.name);

            div.innerHTML = `
                <div class="thumbnail"></div>
                <p id="title-${p.id}">${p.name}</p>
                <span>${p.date}</span>
            `;
            grid.appendChild(div);
        }

        function startPress(e, id) {
            selectedId = id;
            pressTimer = setTimeout(() => {
                showMenu(e);
            }, 600); // 600ms halten für Menü
        }

        function showMenu(e) {
            e.preventDefault();
            menu.style.display = 'block';
            menu.style.left = (e.pageX || e.touches[0].pageX) + 'px';
            menu.style.top = (e.pageY || e.touches[0].pageY) + 'px';
        }

        function openPresentation(name) {
            if (menu.style.display === 'block') return;
            document.getElementById('editorTitle').innerText = name;
            editor.style.display = 'flex';
        }

        function closeEditor() { editor.style.display = 'none'; }

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
