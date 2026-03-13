<!DOCTYPE html>
<html lang="it">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Calcetto Stats Pro</title>
    <style>
        :root { --p: #2563eb; --bg: #f8fafc; --card: #ffffff; --team1: #A6F864; --team2: #1E3E4F; }
        body { font-family: -apple-system, sans-serif; background: var(--bg); margin: 0; padding: 20px; overflow-x: hidden; }
        
        /* Home */
        .header-home { display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; }
        .main-app-title { font-size: 1.5rem; font-weight: 800; border: none; background: transparent; color: #1e293b; width: 100%; outline: none; border-bottom: 2px solid transparent; }
        .main-app-title:focus { border-bottom: 2px solid var(--p); }
        
        .match-card { background: var(--card); padding: 15px; border-radius: 12px; margin-bottom: 10px; box-shadow: 0 2px 4px rgba(0,0,0,0.05); display: flex; justify-content: space-between; align-items: center; cursor: pointer; border: 1px solid #e2e8f0; }
        .match-info { display: flex; flex-direction: column; gap: 4px; flex-grow: 1; }
        .match-main-line { display: flex; align-items: baseline; gap: 10px; }
        .match-title-text { font-weight: bold; font-size: 1.1rem; }
        .match-timestamp { font-size: 0.8rem; color: #64748b; }
        .match-score-line { font-size: 0.9rem; font-weight: bold; color: #1e293b; }
        
        .btn-new { background: var(--p); color: white; border: none; padding: 15px; border-radius: 12px; width: 100%; font-weight: bold; font-size: 1rem; margin-bottom: 20px; }

        .backup-section { margin-top: 30px; padding: 15px; background: #f1f5f9; border-radius: 12px; border: 1px dashed #cbd5e1; text-align: center; }
        .btn-backup { background: #64748b; color: white; border: none; padding: 10px 15px; border-radius: 8px; font-size: 0.75rem; cursor: pointer; margin: 5px; font-weight: bold; }

        /* MODALE */
        #modal-game {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: var(--bg); z-index: 100;
            transform: translateY(100%); transition: transform 0.3s cubic-bezier(0.4, 0, 0.2, 1);
            overflow-y: auto; padding-bottom: 40px;
        }
        #modal-game.open { transform: translateY(0); }
        .modal-header { position: sticky; top: 0; background: var(--bg); padding: 15px; border-bottom: 1px solid #e2e8f0; display: flex; justify-content: space-between; align-items: center; z-index: 101; }
        .content { padding: 15px; max-width: 500px; margin: 0 auto; }

        /* Scoreboard */
        .scoreboard { display: flex; justify-content: space-around; align-items: center; background: white; padding: 20px; border-radius: 15px; margin-bottom: 15px; box-shadow: 0 4px 6px -1px rgba(0,0,0,0.1); }
        .score-val { font-size: 3rem; font-weight: 800; width: 70px; border: none; text-align: center; background: #f1f5f9; border-radius: 10px; }
        .team-name-input { font-weight: bold; border: none; border-bottom: 2px solid #e2e8f0; text-align: center; font-size: 0.9rem; padding: 5px; width: 110px; background: transparent; }

        .player-list-box { background: white; border-radius: 12px; padding: 15px; margin-bottom: 15px; border: 2px solid #eee; }
        .box-team-a { border-color: var(--team1); }
        .box-team-b { border-color: var(--team2); }
        
        .label-team { display: inline-block; padding: 4px 12px; border-radius: 20px; font-size: 0.8rem; font-weight: 800; text-transform: uppercase; margin-bottom: 10px; }
        .label-a { background: var(--team1); color: #000; }
        .label-b { background: var(--team2); color: #fff; }

        .player-row { display: flex; justify-content: space-between; align-items: center; padding: 10px 0; border-bottom: 1px solid #f1f5f9; }
        .btn-inc { width: 36px; height: 36px; border-radius: 50%; border: none; background: #f1f5f9; font-size: 1.2rem; font-weight: bold; }
        .btn-plus { background: #dcfce7; color: #166534; }
        .btn-del-p { background: #fee2e2; border: none; color: #ef4444; font-weight: bold; padding: 8px 12px; border-radius: 8px; cursor: pointer; font-size: 0.9rem; margin-right: 10px; }
        
        textarea { width: 100%; border: 1px solid #e2e8f0; border-radius: 10px; padding: 12px; box-sizing: border-box; font-family: inherit; font-size: 0.9rem; resize: none; }
        
        /* Classifica Goal */
        .stats-container { display: flex; gap: 10px; flex-wrap: wrap; margin-top: 10px; }
        .stats-box { flex: 1; min-width: 140px; background: #fff; padding: 10px; border-radius: 10px; border-left: 4px solid #eee; }
        .stats-box-a { border-left-color: var(--team1); }
        .stats-box-b { border-left-color: var(--team2); }
        .stats-title { font-size: 0.75rem; font-weight: 800; text-transform: uppercase; margin-bottom: 5px; color: #64748b; }
        .stats-row { font-size: 0.85rem; padding: 2px 0; border-bottom: 1px dotted #eee; display: flex; justify-content: space-between; }

        .btn-del { background: #fee2e2; color: #991b1b; border: none; padding: 10px; border-radius: 8px; font-size: 0.8rem; margin-top: 20px; width: 100%; }
    </style>
</head>
<body>

    <div class="header-home">
        <input type="text" id="main-app-title" class="main-app-title" onchange="saveAppTitle()">
    </div>
    <button class="btn-new" onclick="initNewMatch()">+ Nuova Partita</button>
    <div id="match-list"></div>

    <div class="backup-section">
        <small style="display:block; margin-bottom:10px; color:#64748b; font-weight:bold">SALVATAGGIO DATI</small>
        <button class="btn-backup" onclick="exportData()">📥 Esporta Backup</button>
        <button class="btn-backup" onclick="document.getElementById('importFile').click()">📤 Importa Backup</button>
        <input type="file" id="importFile" style="display:none" onchange="importData(event)">
    </div>

    <div id="modal-game">
        <div class="modal-header">
            <input type="text" id="game-title" style="border:none; font-weight:bold; font-size:1.1rem; width:70%; background:transparent" onfocus="this.select()" onchange="sync()">
            <button onclick="closeModal()" style="border:none; background:#eee; padding:8px 15px; border-radius:20px; font-weight:bold">Chiudi</button>
        </div>

        <div class="content">
            <div class="scoreboard">
                <div style="text-align:center">
                    <input type="text" id="t-name-a" class="team-name-input" onchange="sync()">
                    <br><input type="number" id="t-score-a" class="score-val" onchange="manualScore=true; sync()">
                </div>
                <div style="font-size:1.5rem; color:#94a3b8">VS</div>
                <div style="text-align:center">
                    <input type="text" id="t-name-b" class="team-name-input" onchange="sync()">
                    <br><input type="number" id="t-score-b" class="score-val" onchange="manualScore=true; sync()">
                </div>
            </div>

            <div class="player-list-box box-team-a">
                <span class="label-team label-a" id="label-a">Gialli</span>
                <div id="list-a"></div>
                <button onclick="addPlayer('a')" style="width:100%; border:1px dashed var(--team1); background:none; padding:10px; margin-top:10px; border-radius:8px; font-weight:bold">+ Aggiungi</button>
            </div>

            <div class="player-list-box box-team-b">
                <span class="label-team label-b" id="label-b">Blu</span>
                <div id="list-b"></div>
                <button onclick="addPlayer('b')" style="width:100%; border:1px dashed var(--team2); background:none; padding:10px; margin-top:10px; border-radius:8px; font-weight:bold">+ Aggiungi</button>
            </div>

            <div class="player-list-box" style="border:1px solid #e2e8f0">
                <strong>Note Partita</strong>
                <textarea id="game-notes" rows="4" maxlength="4000" onchange="sync()"></textarea>
                
                <div style="margin-top:20px; border-top:1px solid #eee; padding-top:15px">
                    <strong style="font-size:0.9rem">Riepilogo Marcatori:</strong>
                    <div class="stats-container" id="goal-rank"></div>
                </div>
            </div>

            <button class="btn-new" style="background:#059669; margin-top:10px; margin-bottom:5px" onclick="closeModal()">SALVA E ESCI</button>
            <button class="btn-del" onclick="deleteMatch()">Elimina Partita</button>
        </div>
    </div>

<script>
    let fullData = JSON.parse(localStorage.getItem('calcetto_v13')) || { appTitle: "⚽ Kicker Count", matches: {} };
    let activeId = null;
    let manualScore = false;

    function save() { localStorage.setItem('calcetto_v13', JSON.stringify(fullData)); renderHome(); }
    function saveAppTitle() { fullData.appTitle = document.getElementById('main-app-title').value; save(); }

    function renderHome() {
        document.getElementById('main-app-title').value = fullData.appTitle;
        const list = document.getElementById('match-list');
        list.innerHTML = '';
        Object.keys(fullData.matches).sort((a,b) => b.localeCompare(a)).forEach(id => {
            const m = fullData.matches[id];
            list.innerHTML += `<div class="match-card" onclick="openMatch('${id}')"><div class="match-info"><div class="match-main-line"><span class="match-title-text">${m.title}</span><span class="match-timestamp">${m.date || ''}</span></div><div class="match-score-line">${m.teamA} ${m.scoreA} - ${m.scoreB} ${m.teamB}</div></div><div style="color:#64748b">➔</div></div>`;
        });
    }

    function exportData() {
        const dataStr = JSON.stringify(fullData, null, 2);
        const blob = new Blob([dataStr], { type: "application/json" });
        const url = URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url;
        a.download = `calcetto_backup_${new Date().toISOString().slice(0,10)}.json`;
        a.click();
        URL.revokeObjectURL(url);
    }

    function importData(event) {
        const file = event.target.files[0];
        if (!file) return;
        const reader = new FileReader();
        reader.onload = function(e) {
            try {
                const imported = JSON.parse(e.target.result);
                if (confirm("Sovrascrivere i dati attuali?")) {
                    fullData = imported;
                    save();
                    alert("Dati ripristinati!");
                }
            } catch (err) { alert("File non valido."); }
        };
        reader.readAsText(file);
    }

    function initNewMatch() {
        const title = prompt("Nome della partita:", "Partita");
        if(!title) return;
        const now = new Date();
        const dateStr = now.toLocaleDateString() + " " + now.toLocaleTimeString([], {hour:'2-digit', minute:'2-digit'});
        const id = 'id_' + now.getTime();
        const sortedKeys = Object.keys(fullData.matches).sort((a,b) => b.localeCompare(a));
        let pA = [], pB = [], tA = "Gialli", tB = "Blu";
        if(sortedKeys.length > 0) {
            let msg = "Importa da:\n";
            sortedKeys.slice(0,5).forEach((k,i) => msg += `${i+1}) ${fullData.matches[k].title}\n`);
            const choice = parseInt(prompt(msg)) - 1;
            if(!isNaN(choice) && sortedKeys[choice]) {
                const src = fullData.matches[sortedKeys[choice]];
                pA = src.playersA.map(p => ({n:p.n, g:0})); pB = src.playersB.map(p => ({n:p.n, g:0}));
                tA = src.teamA; tB = src.teamB;
            }
        }
        fullData.matches[id] = { title, date: dateStr, teamA: tA, teamB: tB, scoreA: 0, scoreB: 0, playersA: pA, playersB: pB, notes: "" };
        save(); openMatch(id);
    }

    function openMatch(id) {
        activeId = id; const m = fullData.matches[id];
        document.getElementById('game-title').value = m.title;
        document.getElementById('t-name-a').value = m.teamA; document.getElementById('t-name-b').value = m.teamB;
        document.getElementById('game-notes').value = m.notes;
        manualScore = false; renderGame();
        document.getElementById('modal-game').classList.add('open');
    }

    function renderGame() {
        if(!activeId) return;
        const m = fullData.matches[activeId];
        document.getElementById('label-a').innerText = m.teamA; document.getElementById('label-b').innerText = m.teamB;
        if(!manualScore) {
            m.scoreA = m.playersA.reduce((a,b) => a + b.g, 0); m.scoreB = m.playersB.reduce((a,b) => a + b.g, 0);
        }
        document.getElementById('t-score-a').value = m.scoreA; document.getElementById('t-score-b').value = m.scoreB;
        
        ['a','b'].forEach(side => {
            const list = side === 'a' ? m.playersA : m.playersB;
            document.getElementById('list-'+side).innerHTML = list.map((p, i) => `
                <div class="player-row">
                    <div style="display:flex; align-items:center">
                        <button class="btn-del-p" onclick="delP('${side}',${i},'${p.n}')">✕</button>
                        <span>${p.n}</span>
                    </div>
                    <div style="display:flex; align-items:center; gap:12px">
                        <button class="btn-inc" onclick="upG('${side}',${i},-1)">-</button>
                        <strong style="min-width:15px; text-align:center">${p.g}</strong>
                        <button class="btn-inc btn-plus" onclick="upG('${side}',${i},1)">+</button>
                    </div>
                </div>`).join('');
        });

        // Rendering Classifica Goal
        const rankDiv = document.getElementById('goal-rank');
        const getRankHtml = (teamName, players, styleClass) => {
            const sorted = [...players].sort((a,b) => b.g - a.g);
            let html = `<div class="stats-box ${styleClass}"><div class="stats-title">${teamName}</div>`;
            sorted.forEach((p, i) => {
                html += `<div class="stats-row"><span>${i+1}) ${p.n}</span> <strong>${p.g}</strong></div>`;
            });
            return html + `</div>`;
        };
        rankDiv.innerHTML = getRankHtml(m.teamA, m.playersA, 'stats-box-a') + getRankHtml(m.teamB, m.playersB, 'stats-box-b');
        
        save();
    }

    function delP(side, index, playerName) {
        if (confirm(`Eliminare ${playerName}?`)) {
            const list = (side === 'a') ? fullData.matches[activeId].playersA : fullData.matches[activeId].playersB;
            list.splice(index, 1);
            manualScore = false; renderGame();
        }
    }

    function upG(s, i, v) {
        const pList = (s === 'a') ? fullData.matches[activeId].playersA : fullData.matches[activeId].playersB;
        pList[i].g = Math.max(0, pList[i].g + v); manualScore = false; renderGame();
    }

    function addPlayer(s) {
        const n = prompt("Nome:");
        if(n) { (s==='a'?fullData.matches[activeId].playersA : fullData.matches[activeId].playersB).push({n:n, g:0}); renderGame(); }
    }

    function sync() {
        const m = fullData.matches[activeId];
        m.title = document.getElementById('game-title').value;
        m.teamA = document.getElementById('t-name-a').value; m.teamB = document.getElementById('t-name-b').value;
        m.scoreA = parseInt(document.getElementById('t-score-a').value) || 0;
        m.scoreB = parseInt(document.getElementById('t-score-b').value) || 0;
        m.notes = document.getElementById('game-notes').value;
        renderGame();
    }

    function closeModal() { document.getElementById('modal-game').classList.remove('open'); activeId = null; }
    function deleteMatch() { if(confirm("Eliminare definitivamente?")) { delete fullData.matches[activeId]; save(); closeModal(); } }

    renderHome();
</script>
</body>
</html>
