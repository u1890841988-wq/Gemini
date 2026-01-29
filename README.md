<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Modern Keynote</title>
    <style>
        :root {
            --apple-blue: #007aff;
            --bg-dark: #1c1c1e;
            --glass: rgba(255, 255, 255, 0.1);
        }

        * { box-sizing: border-box; margin: 0; padding: 0; font-family: "SF Pro", -apple-system, sans-serif; }

        body {
            background: linear-gradient(135deg, #1e40af, #3b82f6);
            height: 100vh; display: flex; flex-direction: column; align-items: center; overflow: hidden; color: white;
        }

        /* STARTBILDSCHIRM */
        .hero {
            background: var(--bg-dark); width: 85%; max-width: 900px;
            margin-top: 5vh; padding: 60px; border-radius: 40px;
            text-align: center; box-shadow: 0 40px 100px rgba(0,0,0,0.5); z-index: 5;
        }

        .hero h1 { font-size: clamp(40px, 8vw, 90px); margin-bottom: 40px; font-weight: 700; letter-spacing: -2px; }

        .btn-group { display: flex; flex-direction: column; align-items: center; gap: 15px; }
        .btn {
            width: 300px; padding: 18px; border-radius: 50px; border: none;
            font-size: 18px; font-weight: 500; cursor: pointer; transition: 0.2s;
        }
        .btn-main { background: var(--apple-blue); color: white; }
        .btn-sub { background: #2c2c2e; color: white; }

        /* DASHBOARD */
        .dashboard {
            background: rgba(20, 20, 22, 0.7); backdrop-filter: blur(50px);
            width: 95%; max-width: 1100px; height: 50vh;
            margin-top: -50px; border-radius: 40px; padding: 40px;
            border: 1px solid rgba(255,255,255,0.1); overflow-y: auto;
        }

        .grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(180px, 1fr)); gap: 30px; }
        .card { text-align: center; cursor: pointer; transition: 0.2s; }
        .card:active { transform: scale(0.95); }
        .thumb { width: 100%; aspect-ratio: 16/10; background: #333; border-radius: 12px; margin-bottom: 10px; box-shadow: 0 10px 20px rgba(0,0,0,0.3); }

        /* EDITOR (FUNKTIONAL) */
        #editor {
            position: fixed; inset: 0; background: #000; z-index: 100;
            display: none; flex-direction: column;
        }

        .toolbar {
            height: 60px; background: #1c1c1e; display: flex; 
            justify-content: space-between; align-items: center; padding: 0 25px;
            border-bottom: 1px solid #333;
        }

        .editor-body { display: flex; flex-grow: 1; }
        .sidebar { width: 220px; background: #161617; border-right: 1px solid #333; padding: 20px; }
        .mini-slide { width: 100%; aspect-ratio: 16/10; background: white; border: 3px solid var(--apple-blue); border-radius: 8px; }

        .canvas { flex-grow: 1; display: flex; justify-content: center; align-items: center; padding: 40px; background: #111; }
        
        /* Das ist jetzt ein echtes Textfeld! */
        .slide {
            width: 100%; max-width: 900px; aspect-ratio: 16/9; background: white;
            color: black; padding: 80px; box-shadow: 0 20px 60px rgba(0,0,0,0.8);
            outline: none; text-align: left;
        }

        .slide h2 { font-size: 50px; margin-bottom: 20px; outline: none; }
        .slide p { font-size: 24px; color: #666; outline: none; }

        #contextMenu {
            position: fixed; background: #2c2c2e; border-radius: 12px;
            width: 180px; display: none; z-index: 200; border: 1px solid #444;
        }
        .menu-item { padding: 15px; border-bottom: 1px solid #444; cursor: pointer; }
    </style>
</head>
<body>

    <div id="home">
        <div class="hero">
            <h1>Keynote</h1>
            <div class="btn-group">
                <button class="btn btn-main" onclick="createProject()">Thema auswählen</button>
                <button class="btn btn-sub">Gliederung starten</button>
            </div>
        </div>
        <div class="dashboard">
            <div id="grid" class="grid"></div>
        </div>
    </div>

    <div id="editor">
        <div class="toolbar">
            <div style="color: var(--apple-blue); cursor: pointer;" onclick="closeEditor()">❮ Fertig</div>
            <div id="currentTitle" style="font-weight: 600;">Unbenannt</div>
            <div style="display: flex; gap: 20px;">
                <button onclick="document.execCommand('bold')" style="background:none; border:none; color:white; font-size:20px;"><b>B</b></button>
                <button onclick="saveSlide()" style="color: var(--apple-blue); background:none; border:none;">Speichern</button>
            </div>
        </div>
        <div class="editor-body">
            <div class="sidebar">
                <div class="mini-slide"></div>
            </div>
            <div class="canvas">
                <div class="slide" contenteditable="true" id="mainSlide">
                    <h2 id="sTitle">Titel eingeben</h2>
                    <p id="sSub">Untertitel hier tippen...</p>
                </div>
            </div>
        </div>
    </div>

    <div id="contextMenu">
        <div class="menu-item" onclick="deleteProject()">Löschen</div>
        <div class="menu-item" onclick="renameProject()">Umbenennen</div>
    </div>

    <script>
        let projects = JSON.parse(localStorage.getItem('projects') || '[]');
        let currentId = null;

        function render() {
            const grid = document.getElementById('grid');
            grid.innerHTML = '';
            projects.forEach(p => {
                const div = document.createElement('div');
                div.className = 'card';
                div.innerHTML = `<div class="thumb"></div><p>${p.name}</p><span style="font-size:12px; color:#888;">${p.date}</span>`;
                div.onclick = () => openEditor(p);
                div.oncontextmenu = (e) => {
                    e.preventDefault();
                    currentId = p.id;
                    const menu = document.getElementById('contextMenu');
                    menu.style.display = 'block';
                    menu.style.left = e.pageX + 'px';
                    menu.style.top = e.pageY + 'px';
                };
                grid.appendChild(div);
            });
        }

        function createProject() {
            const name = prompt("Projektname:");
            if(!name) return;
            const newP = { id: Date.now(), name: name, date: new Date().toLocaleDateString(), content: "<h2>"+name+"</h2><p>Starten Sie hier...</p>" };
            projects.push(newP);
            localStorage.setItem('projects', JSON.stringify(projects));
            render();
        }

        function openEditor(p) {
            currentId = p.id;
            document.getElementById('currentTitle').innerText = p.name;
            document.getElementById('mainSlide').innerHTML = p.content;
            document.getElementById('editor').style.display = 'flex';
        }

        function saveSlide() {
            const content = document.getElementById('mainSlide').innerHTML;
            const p = projects.find(x => x.id === currentId);
            p.content = content;
            localStorage.setItem('projects', JSON.stringify(projects));
            alert("Gespeichert!");
        }

        function closeEditor() { document.getElementById('editor').style.display = 'none'; }
        
        function deleteProject() {
            projects = projects.filter(x => x.id !== currentId);
            localStorage.setItem('projects', JSON.stringify(projects));
            render();
        }

        window.onclick = () => document.getElementById('contextMenu').style.display = 'none';
        render();
    </script>
</body>
</html>
