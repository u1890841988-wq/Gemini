<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Smart Task Planner</title>
    <style>
        :root {
            --primary: #2563eb;
            --bg: #f8fafc;
            --text: #1e293b;
        }

        body {
            font-family: 'Segoe UI', system-ui, sans-serif;
            background-color: var(--bg);
            color: var(--text);
            display: flex;
            justify-content: center;
            padding: 2rem;
        }

        .container {
            background: white;
            padding: 2rem;
            border-radius: 12px;
            box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1);
            width: 100%;
            max-width: 400px;
        }

        h2 { margin-top: 0; color: var(--primary); }

        .input-group {
            display: flex;
            gap: 10px;
            margin-bottom: 1.5rem;
        }

        input {
            flex: 1;
            padding: 10px;
            border: 1px solid #cbd5e1;
            border-radius: 6px;
            outline: none;
        }

        button#add-btn {
            background: var(--primary);
            color: white;
            border: none;
            padding: 10px 15px;
            border-radius: 6px;
            cursor: pointer;
        }

        ul { list-style: none; padding: 0; }

        li {
            background: #f1f5f9;
            margin-bottom: 8px;
            padding: 10px;
            border-radius: 6px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            animation: fadeIn 0.3s ease;
        }

        .completed { text-decoration: line-through; opacity: 0.6; }

        .delete-btn {
            background: #ef4444;
            color: white;
            border: none;
            padding: 5px 10px;
            border-radius: 4px;
            cursor: pointer;
            font-size: 0.8rem;
        }

        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }
    </style>
</head>
<body>

<div class="container">
    <h2>🎯 Task Planner</h2>
    <div class="input-group">
        <input type="text" id="taskInput" placeholder="Was steht an?">
        <button id="add-btn">Hinzufügen</button>
    </div>
    <ul id="taskList"></ul>
</div>

<script>
    const input = document.getElementById('taskInput');
    const btn = document.getElementById('add-btn');
    const list = document.getElementById('taskList');

    // 1. Lade gespeicherte Tasks beim Start
    document.addEventListener('DOMContentLoaded', getTasks);

    // 2. Task hinzufügen
    btn.addEventListener('click', () => {
        if (input.value === "") return;
        createTaskElement(input.value);
        saveLocal(input.value);
        input.value = "";
    });

    function createTaskElement(text) {
        const li = document.createElement('li');
        li.innerHTML = `
            <span onclick="this.parentElement.classList.toggle('completed')">${text}</span>
            <button class="delete-btn" onclick="removeTask(this)">Löschen</button>
        `;
        list.appendChild(li);
    }

    // 3. Im LocalStorage speichern
    function saveLocal(task) {
        let tasks = localStorage.getItem('tasks') ? JSON.parse(localStorage.getItem('tasks')) : [];
        tasks.push(task);
        localStorage.setItem('tasks', JSON.stringify(tasks));
    }

    // 4. Tasks aus Speicher laden
    function getTasks() {
        let tasks = localStorage.getItem('tasks') ? JSON.parse(localStorage.getItem('tasks')) : [];
        tasks.forEach(task => createTaskElement(task));
    }

    // 5. Löschen
    function removeTask(btn) {
        const taskText = btn.parentElement.firstChild.textContent.trim();
        let tasks = JSON.parse(localStorage.getItem('tasks'));
        const newTasks = tasks.filter(t => t !== taskText);
        localStorage.setItem('tasks', JSON.stringify(newTasks));
        btn.parentElement.remove();
    }
</script>

</body>
</html>
