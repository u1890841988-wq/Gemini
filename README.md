<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Keynote Pro Edit</title>
    <style>
        :root { --apple-blue: #007aff; --bg-dark: #1c1c1e; }
        * { box-sizing: border-box; margin: 0; padding: 0; font-family: "SF Pro", sans-serif; }
        body { background: #000; height: 100vh; color: white; overflow: hidden; }

        /* --- DASHBOARD --- */
        #home { display: flex; flex-direction: column; align-items: center; height: 100%; background: radial-gradient(circle at top, #3b82f6, #1e3a8a); padding-top: 20px; }
        .hero { background: var(--bg-dark); width: 98%; max-width: 1600px; padding: 30px; border-radius: 20px; text-align: center; margin-bottom: 20px; }
        .btn-main { background: var(--apple-blue); color: white; padding: 12px 30px; border-radius: 30px; border: none; cursor: pointer; }

        /* --- EDITOR --- */
        #editor { position: fixed; inset: 0; background: #000; display: none; flex-direction: column; z-index: 100; }
        
        /* Toolbar mit Formatierungs-Optionen */
        .toolbar { 
            height: 70px; background: #1c1c1e; display: flex; 
            justify-content: space-between; align-items: center; 
            padding: 0 20px; border-bottom: 1px solid #333; 
        }
        
        .format-controls { display: flex; gap: 10px; align-items: center; background: #2c2c2e; padding: 5px 15px; border-radius: 10px; }
        .format-controls input, .format-controls select { background: #444; color: white; border: none; padding: 5px; border-radius: 5px; }

        .editor-body { display: flex; flex-grow: 1; overflow: hidden; }
        .sidebar { width: 200px; background: #161617; border-right: 1px solid #333; padding: 15px; display: flex; flex-direction: column; gap: 10px; }
        .slide-nav-item { width: 100%; aspect-ratio: 16/10; background: white; border-radius: 4px; cursor: pointer; border: 3px solid transparent; color: black; font-size: 10px; padding: 5px; }
        .slide-nav-item.active { border-color: var(--apple-blue); }

        .canvas-area { flex-grow: 1; background: #111; display: flex; justify-content: center; align-items: center; position: relative; }
        .slide-surface { width: 90%; aspect-ratio: 16/9; background: white; position: relative; box-shadow: 0 0 40px rgba(0,0,0,0.5); }
        
        .text-box { 
            position: absolute; border: 1px dashed transparent; 
            min-width: 50px; color: black; cursor: move; outline: none; padding: 5px;
        }
        .text-box:hover { border-color: #ccc; }
        .text-box:focus { border: 1px solid var(--apple-blue); }
    </style>
</head>
<body>

    <div id="home">
        <div class="hero">
            <h1 style="font-size: 50px; margin-bottom: 15px;">Keynote</h1>
            <button class="btn-main" onclick="createNewProject()">Thema auswählen</button>
        </div>
        <div style="width: 98%; max-width: 1600px; flex-grow: 1; padding: 20px;" id="grid" class="grid"></div>
    </div>

    <div id="editor">
        <div class="toolbar">
            <div style="color:var(--apple-blue); cursor:pointer; width: 100px;" onclick="exitEditor()">❮ Fertig</div>
            
            <div class="format-controls">
                <button onclick="exec('bold')" style="font-weight:bold; width:30px;">B</button>
                <select onchange="exec('fontSize', this.value)">
                    <option value="3">Größe</option>
                    <option value="1">Klein</option>
                    <option value="3">Normal</option>
                    <option value="5">Groß</option>
                    <option value="7">Riesig</option>
                </select>
                <input type="color" onchange="exec('foreColor', this.value)" title="Schriftfarbe">
                <button onclick="addTextBox()" style="background:var(--apple-blue); color:white; border:none; padding:5px 10px; border-radius:5px;">+ Text</button>
            </div>

            <div style="color:var(--apple-blue); cursor:pointer; width: 100px; text-align: right;" onclick="saveCurrentData()">Speichern</div>
        </div>

        <div class="editor-body">
            <div class="sidebar" id="slideSidebar">
                <button onclick="addNewSlide()" style="background:#333; color:white; border:none; padding:8px; border-radius:5px;">+ Neue Folie</button>
            </div>
            <div class="canvas-area">
                <div id="activeSlide" class="slide-surface"></div>
            </div>
        </div>
    </div>

    <script>
        let projects = JSON.parse(localStorage.getItem('keynote_v3') || '[]');
        let currentProject = null;
        let currentSlideIdx = 0;

        function exec(cmd, val = null) { document.execCommand(cmd, false, val); }

        function createNewProject() {
            const name = prompt("Name:");
            if(!name) return;
            projects.push({ id: Date.now(), name, slides: [{ html: '<div class="text-box" style="top:20%; left:10%; font-size:48px;" contenteditable="true">Titel</div>' }] });
            saveAll(); renderDashboard();
        }

        function renderDashboard() {
            const grid = document.getElementById('grid'); grid.innerHTML = '';
            projects.forEach(p => {
                const div = document.createElement('div');
                div.style = "background:#2c2c2e; padding:20px; border-radius:15px; text-align:center; cursor:pointer; display:inline-block; margin:10px; width:200px;";
                div.innerHTML = `<div style="width:100%; height:100px; background:#444; border-radius:8px; margin-bottom:10px;"></div><p>${p.name}</p>`;
                div.onclick = () => openEditor(p);
                grid.appendChild(div);
            });
        }

        function openEditor(proj) {
            currentProject = proj; currentSlideIdx = 0;
            document.getElementById('editor').style.display = 'flex';
            renderEditor();
        }

        function renderEditor() {
            const sidebar = document.getElementById('slideSidebar');
            sidebar.innerHTML = '<button onclick="addNewSlide()" style="background:#333; color:white; border:none; padding:8px; border-radius:5px; margin-bottom:10px;">+ Neue Folie</button>';
            currentProject.slides.forEach((s, idx) => {
                const nav = document.createElement('div');
                nav.className = `slide-nav-item ${idx === currentSlideIdx ? 'active' : ''}`;
                nav.innerHTML = `Folie ${idx + 1}`;
                nav.onclick = () => { saveCurrentData(false); currentSlideIdx = idx; renderEditor(); };
                sidebar.appendChild(nav);
            });
            document.getElementById('activeSlide').innerHTML = currentProject.slides[currentSlideIdx].html;
            makeDraggable();
        }

        function addNewSlide() {
            saveCurrentData(false);
            currentProject.slides.push({ html: '<div class="text-box" style="top:10%; left:10%;" contenteditable="true">Neue Seite</div>' });
            currentSlideIdx = currentProject.slides.length - 1;
            renderEditor();
        }

        function addTextBox() {
            const box = document.createElement('div');
            box.className = 'text-box'; box.style.top = '40%'; box.style.left = '40%';
            box.contentEditable = true; box.innerText = 'Text tippen';
            document.getElementById('activeSlide').appendChild(box);
            makeDraggable();
        }

        function makeDraggable() {
            document.querySelectorAll('.text-box').forEach(box => {
                box.onmousedown = function(e) {
                    if (e.target !== box) return;
                    let sX = e.clientX - box.offsetLeft; let sY = e.clientY - box.offsetTop;
                    document.onmousemove = (e) => {
                        box.style.left = e.clientX - sX + 'px';
                        box.style.top = e.clientY - sY + 'px';
                    };
                    document.onmouseup = () => { document.onmousemove = null; };
                };
            });
        }

        function saveCurrentData(showMsg = true) {
            currentProject.slides[currentSlideIdx].html = document.getElementById('activeSlide').innerHTML;
            saveAll();
            if(showMsg) alert("Gespeichert");
        }

        function saveAll() { localStorage.setItem('keynote_v3', JSON.stringify(projects)); }
        function exitEditor() { saveCurrentData(false); document.getElementById('editor').style.display = 'none'; }
        renderDashboard();
    </script>
</body>
</html>
