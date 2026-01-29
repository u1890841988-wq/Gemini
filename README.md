<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Wide Keynote Pro</title>
    <style>
        :root {
            --apple-blue: #007aff;
            --bg-dark: #1c1c1e;
        }

        * { box-sizing: border-box; margin: 0; padding: 0; font-family: "SF Pro", -apple-system, sans-serif; }

        body {
            background: linear-gradient(135deg, #1e40af, #3b82f6);
            height: 100vh; display: flex; flex-direction: column; align-items: center; overflow: hidden; color: white;
        }

        /* --- STARTBILDSCHIRM (Extrem breit gezogen) --- */
        .hero {
            background: var(--bg-dark); 
            width: 96%; /* Geht fast bis an den Rand */
            max-width: 1400px;
            margin-top: 3vh; 
            padding: 50px; 
            border-radius: 30px;
            text-align: center; 
            box-shadow: 0 40px 100px rgba(0,0,0,0.5); 
            z-index: 5;
        }

        .hero h1 { font-size: 100px; margin-bottom: 30px; font-weight: 700; letter-spacing: -3px; }

        .btn-group { display: flex; justify-content: center; gap: 20px; }
        .btn {
            width: 320px; padding: 18px; border-radius: 50px; border: none;
            font-size: 19px; font-weight: 500; cursor: pointer;
        }
        .btn-main { background: var(--apple-blue); color: white; }
        .btn-sub { background: #2c2c2e; color: white; }

        /* --- DASHBOARD (Ebenfalls ultra breit) --- */
        .dashboard {
            background: rgba(20, 20, 22, 0.7); 
            backdrop-filter: blur(60px) saturate(180%);
            -webkit-backdrop-filter: blur(60px) saturate(180%);
            width: 98%; 
            max-width: 1500px; 
            height: 55vh;
            margin-top: -40px; 
            border-radius: 40px; 
            padding: 60px 40px;
            border: 1px solid rgba(255,255,255,0.1); 
            overflow-y: auto;
        }

        .grid { 
            display: grid; 
            grid-template-columns: repeat(auto-fill, minmax(200px, 1fr)); 
            gap: 40px; 
        }

        .card { text-align: center; cursor: pointer; }
        .thumb { 
            width: 100%; aspect-ratio: 16/10; background: #333; 
            border-radius: 8px; margin-bottom: 12px; 
            box-shadow: 0 15px 30px rgba(0,0,0,0.4); 
        }

        /* --- EDITOR (Maximale Fläche) --- */
        #editor {
            position: fixed; inset: 0; background: #000; z-index: 100;
            display: none; flex-direction: column;
        }

        .toolbar {
            height: 60px; background: #1c1c1e; display: flex; 
            justify-content: space-between; align-items: center; padding: 0 30px;
        }

        .editor-body { display: flex; flex-grow: 1; }
        .sidebar { width: 200px; background: #161617; border-right: 1px solid #333; padding: 20px; }
        
        .canvas { 
            flex-grow: 1; background: #000; 
            display: flex; justify-content: center; align-items: center; 
        }

        .slide {
            width: 92%; height: 85%; background: white;
            color: black; padding: 5%; box-shadow: 0 0 50px rgba(0,0,0,1);
            outline: none;
        }
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
            <div id="currentTitle" style="font-weight: 600;">Projekt</div>
            <div style="color: var(--apple-blue);" onclick="saveSlide()">Speichern</div>
        </div>
        <div class="editor-body">
            <div class="sidebar">
                <div style="width:100%; aspect-ratio:16/10; background:white; border:2px solid var(--apple-blue); border-radius:4px;"></div>
            </div>
            <div class="canvas">
                <div class="slide" contenteditable="true" id="mainSlide">
                    <h2 style="font-size: 60px;">Titel hier</h2>
                    <p style="font-size: 30px; color: #555;">Klicken zum Tippen...</p>
                </div>
            </div>
        </div>
    </div>

    <script>
        let projects = JSON.parse(localStorage.getItem('projectsWide') || '[]');
        let currentId = null;

        function render() {
            const grid = document.getElementById('grid');
            grid.innerHTML = '';
            projects.forEach(p => {
                const div = document.createElement('div');
                div.className = 'card';
                div.innerHTML = `<div class="thumb"></div><p style="font-weight:500;">${p.name}</p>`;
                div.onclick = () => openEditor(p);
                grid.appendChild(div);
            });
        }

        function createProject() {
            const name = prompt("Name:");
            if(!name) return;
            const newP = { id: Date.now(), name: name, content: "<h2>"+name+"</h2><p>Inhalt...</p>" };
            projects.push(newP);
            localStorage.setItem('projectsWide', JSON.stringify(projects));
            render();
        }

        function openEditor(p) {
            currentId = p.id;
            document.getElementById('currentTitle').innerText = p.name;
            document.getElementById('mainSlide').innerHTML = p.content;
            document.getElementById('editor').style.display = 'flex';
        }

        function saveSlide() {
            const p = projects.find(x => x.id === currentId);
            p.content = document.getElementById('mainSlide').innerHTML;
            localStorage.setItem('projectsWide', JSON.stringify(projects));
            alert("Gespeichert");
        }

        function closeEditor() { document.getElementById('editor').style.display = 'none'; }
        render();
    </script>
</body>
</html>
