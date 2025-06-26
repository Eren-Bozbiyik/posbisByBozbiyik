<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <title>POSBİS- Postür Bilgi ve Takip Sistemi</title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <script src="https://cdn.plot.ly/plotly-latest.min.js"></script>
    <link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Roboto:400,500,700&display=swap">
    <style>
        :root {
            --sidebar-width: 14vw;
            --active-bg: #e3eaf6;
            --sidebar-bg: #f4f7fb;
            --sidebar-border: 2px solid #8b8b8b;
            --main-bg: #fff;
            --center-width: 62vw;
            --fsr-width: 27vw;
            --fsr-bubble-size: 32px;
            --wall: 2px solid #8b8b8b;
            --main-blue: #1976d2;
            --main-red: #d32f2f;
        }
        html, body {
            height: 100%;
            margin: 0;
            padding: 0;
            background: #f2f2f2;
            font-family: 'Roboto', Arial, sans-serif;
            width: 100vw;
            box-sizing: border-box;
        }
        body {
            min-height: 100vh;
            width: 100vw;
        }
        .appbar {
            width: 100vw;
            height: 36px;
            background: #232a35;
            color: #e3eaf6;
            font-size: 1.07em;
            display: flex;
            align-items: center;
            letter-spacing: 0.08em;
            padding-left: 24px;
            box-sizing: border-box;
            font-weight: 500;
            border-bottom: 1.5px solid #1976d2;
            position: fixed;
            top: 0; left: 0;
            z-index: 100;
            min-height: 28px;
            user-select: none;
        }
        .dashboard-main {
            display: flex;
            flex-direction: row;
            height: calc(100vh - 36px);
            margin-top: 36px;
            width: 100vw;
        }
        .sidebar {
            width: var(--sidebar-width);
            min-width: 110px;
            background: var(--sidebar-bg);
            border-right: var(--sidebar-border);
            display: flex;
            flex-direction: column;
            align-items: stretch;
            padding: 0;
            box-sizing: border-box;
            height: 100%;
        }
        .sidebar-header {
            padding: 20px 0 12px 0;
            text-align: center;
            font-weight: bold;
            color: #1976d2;
            letter-spacing: 0.04em;
            font-size: 1.11em;
            border-bottom: 1px solid #d0daf1;
        }
        .conversation-list {
            flex: 1 1 auto;
            overflow-y: auto;
            margin: 0; padding: 0;
            list-style: none;
            background: var(--sidebar-bg);
        }
        .conversation-item {
            display: flex;
            align-items: center;
            padding: 13px 16px 13px 18px;
            border-bottom: 1px solid #e3eaf6;
            cursor: pointer;
            color: #333;
            background: transparent;
            font-size: 1.02em;
            transition: background 0.13s;
        }
        .conversation-item.active, .conversation-item:hover {
            background: var(--active-bg);
            color: #1976d2;
        }
        .conversation-title {
            flex: 1 1 auto;
            overflow: hidden;
            white-space: nowrap;
            text-overflow: ellipsis;
        }
        .conversation-delete {
            margin-left: 7px;
            color: #d32f2f;
            font-size: 1.18em;
            cursor: pointer;
            opacity: 0.7;
            transition: opacity 0.14s;
        }
        .conversation-delete:hover {
            opacity: 1;
        }
        .sidebar-footer {
            border-top: 1px solid #d0daf1;
            padding: 12px 0 18px 0;
            text-align: center;
        }
        .add-conv-btn {
            background: #1976d2;
            color: #fff;
            border: none;
            border-radius: 6px;
            padding: 7px 22px;
            font-size: 1em;
            font-weight: 600;
            cursor: pointer;
            transition: background 0.16s;
        }
        .add-conv-btn:hover { background: #0d3570; }
        .main-content {
            flex: 1 1 auto;
            min-width: 0;
            display: flex;
            flex-direction: row;
            background: var(--main-bg);
            height: 100%;
        }
        .center-stack {
            display: flex;
            flex-direction: column;
            border-right: var(--wall);
            height: 100vh;
            width: var(--center-width);
            min-width: 340px;
            background: #f8fafd;
            align-items: stretch;
            justify-content: stretch;
        }
        .center-split {
            display: flex;
            flex-direction: column;
            height: 100%;
            width: 100%;
        }
        .grafik-panel {
            flex: 12 0 0%;
            display: flex;
            flex-direction: column;
            background: #fff;
            box-sizing: border-box;
            padding-top: 0 !important;
        }
        .kisi-bilgi-row {
            display: flex;
            flex-direction: row;
            align-items: center;
            justify-content: space-between;
            padding: 12px 28px 0 30px;
        }
        .kisi-bilgi-paneli {
            color: #1976d2;
            font-size: 1.13em;
            font-weight: 500;
            letter-spacing: 0.01em;
            padding: 0;
        }
        .fullscreen-btn {
            background: none;
            border: none;
            color: #1976d2;
            font-size: 1.5em;
            cursor: pointer;
            margin-right: 10px;
            margin-top: 0px;
            transition: color 0.15s;
        }
        .fullscreen-btn:hover { color: #d32f2f; }
        .plot-border {
            border: 2px solid #8b8b8b;
            background: #fff;
            margin: 3px;
            padding: 0;
            box-sizing: border-box;
            flex: 1 1 0;
            display: flex;
            flex-direction: column;
        }
        .plot-holder {
            width: 100%;
            height: 100%;
            background: #fff;
            margin: 0;
            padding: 0;
            flex: 1 1 0;
            display: flex;
        }
        .fullscreen-plot-holder {
            position: fixed !important;
            left: 0; top: 0; right: 0; bottom: 0;
            width: 100vw !important;
            height: 98vh !important;
            z-index: 9999;
            background: #fff;
            box-shadow: 0 0 0 100vw rgba(0,0,0,0.25);
            margin: 0 !important;
            border-radius: 0 !important;
        }
        .kontrol-panel {
            flex: 3 0 0%;
            background: #fff;
            box-sizing: border-box;
            display: flex;
            align-items: flex-start;
            padding: 30px 3vw 30px 3vw;
            border-top: var(--wall);
        }
        .panel-controls {
            width: 100%;
            background: transparent;
            display: flex;
            flex-direction: row;
            gap: 2vw;
            flex-wrap: wrap;
            justify-content: space-between;
            align-items: flex-end;
        }
        .panel-controls > div {
            flex: 1 1 0;
            display: flex;
            flex-direction: column;
            min-width: 120px;
            max-width: 240px;
        }
        .panel-controls label {
            font-weight: 500;
            color: #1976d2;
            margin-bottom: 3px;
        }
        .panel-controls select,
        .panel-controls input[type="number"],
        .panel-controls input[type="text"] {
            font-size: 1.13em;
            font-family: 'Roboto', monospace;
            border: 1px solid #b3c7e6;
            border-radius: 4px;
            padding: 8px 9px;
            margin-bottom: 5px;
            color: #1976d2;
            background: #f8fafd;
            box-sizing: border-box;
        }
        /* Göster butonu kırmızı */
        .panel-controls button[type="submit"] {
            background: #d32f2f;
            color: #fff;
            border: none;
            padding: 11px 0;
            border-radius: 4px;
            font-weight: 700;
            font-size: 1.08em;
            cursor: pointer;
            margin-top: 4px;
            margin-bottom: 0;
            transition: background 0.18s, box-shadow 0.18s;
            box-shadow: 0 1.5px 7px rgba(211,47,47,0.06);
            letter-spacing: 0.03em;
            width: 180px;
            min-width: 120px;
            max-width: 210px;
            align-self: flex-end;
        }
        .panel-controls button[type="submit"]:hover,
        .panel-controls button[type="submit"]:focus {
            background: #ab1818;
        }
        /* Detaylar butonu MAVİ ve inputlarla aynı hizada */
        .details-toggle-btn {
            background: #e3eaf6;
            color: #1976d2;
            border: 1.5px solid #1976d2;
            border-radius: 4px;
            font-weight: 600;
            font-size: 1.13em;
            padding: 8px 9px;
            margin-bottom: 5px;
            margin-top: 0;
            cursor: pointer;
            transition: background 0.15s, color 0.15s, border 0.15s;
            width: 100%;
            box-sizing: border-box;
        }
        .details-toggle-btn:hover,
        .details-toggle-btn:focus {
            background: #d0daf1;
            color: #0d3570;
            border-color: #0d3570;
        }
        .settings-fields {
            overflow: hidden;
            max-height: 0;
            opacity: 0;
            transition: max-height 0.4s cubic-bezier(.47,1.64,.41,.8), opacity 0.18s;
            margin-top: -9px;
        }
        .settings-fields.open {
            max-height: 200px;
            opacity: 1;
            margin-top: 6px;
        }
        .fsr-harita-panel {
            width: var(--fsr-width);
            min-width: 200px;
            height: 100vh;
            background: #fff;
            box-sizing: border-box;
            display: flex;
            flex-direction: column;
            position: relative;
            overflow: hidden;
            justify-content: flex-start;
        }
        .fsr-harita-header {
           color: #1976d2;
           font-weight: 700;
           font-size: 1.08em;
           padding: 8px 0 6px 18px;
           letter-spacing: 0.2px;
           min-height: 16px;
           background: #fff;
           z-index: 2;
        }
        .fsr-harita-content {
            flex: 1 1 auto;
            width: 100%;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: flex-start;
            position: relative;
            z-index: 1;
            min-height: 260px;
            padding-top: 10px;
        }
        .fsr-map-overlay {
            position: relative;
            width: 222px;
            height: 500px;
            margin: 0 auto;
        }
        .fsr-map-bg {
            width: 100%;
            height: 100%;
            display: block;
            user-select: none;
            pointer-events: none;
        }
        .fsr-bubble {
            position: absolute;
            width: var(--fsr-bubble-size);
            height: var(--fsr-bubble-size);
            border-radius: 50%;
            background: #bbb;
            border: 2px solid #1976d2;
            box-shadow: none;
            transition: background 0.3s;
            z-index: 2;
            transform: translate(-50%, -50%);
        }
        @media (max-width: 1100px) {
            :root {
                --sidebar-width: 95vw;
                --center-width: 95vw;
                --fsr-width: 95vw;
            }
            .appbar { position: relative; }
            .dashboard-main { flex-direction: column; align-items: stretch; height: auto;}
            .side-panel, .center-stack, .center-split, .grafik-panel, .kontrol-panel, .fsr-harita-panel { width: 98vw !important; min-width: 0;}
            .grafik-panel, .kontrol-panel, .fsr-harita-panel { min-width: 0; }
            .center-stack, .center-split { height: auto; }
            .fsr-map-overlay { width: 120px; height: 270px; }
            .fsr-map-bg { width: 100%; height: 100%; }
            .plot-holder { min-height: 180px;}
            .panel-controls button { max-width: 99vw; }
        }
    </style>
</head>
<body>
    <div class="appbar">POSBİS-Postür Bilgi ve Takip Sistemi by Bozbıyık</div>
    <div class="dashboard-main">
        <aside class="sidebar">
            <div class="sidebar-header">Sekmeler</div>
            <ul id="convList" class="conversation-list"></ul>
            <div class="sidebar-footer">
                <button class="add-conv-btn" onclick="addConversation()">+ Yeni Sekme</button>
            </div>
        </aside>
        <main class="main-content" id="mainContent">
            <div id="tabsContainer" style="width:100%;height:100%;"></div>
        </main>
    </div>
    <script>
    const FSR_COORDS = {
        fsr3: {x:130, y:65},
        fsr1: {x:110, y:420},
        fsr2: {x:165, y:420}
    };

    let conversations = [
        {
            id: 1,
            title: "Sekme 1",
            settingsOpen: false,
            aralik: "5g",
            sourceUrl: "",
            gapratio: 30,
            gapfactor: 2,
            fsr: { fsr1: NaN, fsr2: NaN, fsr3: NaN }
        }
    ];
    let convCounter = 1, activeConv = 1;

    function renderConversations() {
        const list = document.getElementById('convList');
        list.innerHTML = '';
        for(const conv of conversations) {
            let li = document.createElement('li');
            li.className = 'conversation-item' + (conv.id === activeConv ? ' active' : '');
            li.onclick = () => selectConversation(conv.id);
            li.innerHTML = `<span class="conversation-title">${conv.title}</span>
                            <span class="conversation-delete" title="Kapat" onclick="event.stopPropagation();deleteConversation(${conv.id})">&times;</span>`;
            list.appendChild(li);
        }
    }

    function renderMainContent() {
        const main = document.getElementById("mainContent");
        let tabsContainer = document.getElementById("tabsContainer");
        if (!tabsContainer) {
            tabsContainer = document.createElement('div');
            tabsContainer.id = "tabsContainer";
            tabsContainer.style.width = '100%';
            tabsContainer.style.height = '100%';
            main.appendChild(tabsContainer);
        }
        for(const conv of conversations) {
            let tabId = 'tab-content-' + conv.id;
            let tabDiv = document.getElementById(tabId);
            if (!tabDiv) {
                tabDiv = document.createElement('div');
                tabDiv.id = tabId;
                tabDiv.style.display = 'none';
                tabDiv.style.width = '100%';
                tabDiv.style.height = '100%';
                tabDiv.innerHTML = `
        <div style="display:flex;flex-direction:row;height:100%;min-height:500px;">
            <div class="center-stack" style="height:100vh;">
                <div class="center-split">
                    <div class="grafik-panel">
                        <div class="kisi-bilgi-row">
                            <div class="kisi-bilgi-paneli" id="kisiBilgiPaneli${conv.id}">
                                Kişi Ad-Soyadı: <span id="adSoyad${conv.id}">-</span><br>
                                Kişi T.C. Kimlik Numarası: <span id="tcNo${conv.id}">-</span>
                            </div>
                            <button class="fullscreen-btn" id="fullscreenBtn${conv.id}" title="Tam Ekran">&#9974;</button>
                        </div>
                        <div class="plot-border">
                            <div class="plot-holder" id="plot${conv.id}"></div>
                        </div>
                    </div>
                    <div class="kontrol-panel">
                        <form class="panel-controls" 
                            onsubmit="event.preventDefault(); fetchAndPlot(${conv.id}, false);">
                            <div>
                                <label for="aralik${conv.id}">Zaman Aralığı:</label>
                                <select id="aralik${conv.id}">
                                    <option value="1h">Son 1 Saat</option>
                                    <option value="1g">Son 1 Gün</option>
                                    <option value="5g">Son 5 Gün</option>
                                    <option value="1a">Son 1 Ay</option>
                                    <option value="6a">Son 6 Ay</option>
                                    <option value="1y">Son 1 Yıl</option>
                                </select>
                            </div>
                            <div>
                                <label for="sourceUrlInput${conv.id}">Veri Kaynağı:</label>
                                <input type="text" id="sourceUrlInput${conv.id}" value="${conv.sourceUrl}">
                            </div>
                            <div>
                                <button type="button" class="details-toggle-btn" id="settingsToggleBtn${conv.id}" title="Detaylar">
                                    Detaylar
                                </button>
                                <div id="settingsFields${conv.id}" class="settings-fields">
                                    <label for="gapratio${conv.id}">Boşluk Oran %:</label>
                                    <input type="number" id="gapratio${conv.id}" value="${conv.gapratio}" min="5" max="90" step="5">
                                    <label for="gapfactor${conv.id}">Boşluk Katsayısı:</label>
                                    <input type="number" id="gapfactor${conv.id}" value="${conv.gapfactor}" min="1" max="10" step="1">
                                </div>
                            </div>
                            <button id="fetchBtn${conv.id}" type="submit">Göster</button>
                        </form>
                    </div>
                </div>
            </div>
            <div class="fsr-harita-panel">
                <div class="fsr-harita-header">Tabanlık Basma Haritası</div>
                <div class="fsr-harita-content">
                    <div class="fsr-map-overlay" id="fsr-map-overlay-${conv.id}">
                        <img src="tabanlikharita.png" alt="Tabanlık Harita" class="fsr-map-bg">
                        <div class="fsr-bubble" id="bubble-fsr3-${conv.id}" data-bubble="fsr3"></div>
                        <div class="fsr-bubble" id="bubble-fsr1-${conv.id}" data-bubble="fsr1"></div>
                        <div class="fsr-bubble" id="bubble-fsr2-${conv.id}" data-bubble="fsr2"></div>
                    </div>
                </div>
            </div>
        </div>`;
                tabsContainer.appendChild(tabDiv);

                setTimeout(() => {
                    const toggleBtn = document.getElementById("settingsToggleBtn"+conv.id);
                    const settingsFields = document.getElementById("settingsFields"+conv.id);
                    if (toggleBtn && settingsFields) {
                        toggleBtn.onclick = function() {
                            settingsFields.classList.toggle("open");
                        };
                    }
                    document.getElementById("aralik"+conv.id).value = conv.aralik;
                    const fsBtn = document.getElementById("fullscreenBtn"+conv.id);
                    const plotHolder = document.getElementById("plot"+conv.id);
                    if (fsBtn && plotHolder) {
                        fsBtn.onclick = function() {
                            plotHolder.classList.toggle("fullscreen-plot-holder");
                            fetchAndPlot(conv.id, plotHolder.classList.contains("fullscreen-plot-holder"));
                        }
                    }
                    document.addEventListener("keydown", function escHandler(e){
                        if(e.key==="Escape" && plotHolder && plotHolder.classList.contains("fullscreen-plot-holder")){
                            plotHolder.classList.remove("fullscreen-plot-holder");
                            fetchAndPlot(conv.id, false);
                        }
                    });
                    positionFSRBubbles(conv.id);
                }, 0);

                setTimeout(()=>drawEmptyPlot("plot"+conv.id, false, window.innerWidth<700), 0);
                setTimeout(()=>fetchAndPlot(conv.id, false), 100);
            }
        }
        for(const conv of conversations) {
            let tabId = 'tab-content-' + conv.id;
            let tabDiv = document.getElementById(tabId);
            if (tabDiv) {
                tabDiv.style.display = (conv.id === activeConv) ? '' : 'none';
            }
        }
    }

    function positionFSRBubbles(convId) {
        for (const fsr of ['fsr1','fsr2','fsr3']) {
            const el = document.getElementById(`bubble-${fsr}-${convId}`);
            if (!el) continue;
            el.style.left = FSR_COORDS[fsr].x + "px";
            el.style.top = FSR_COORDS[fsr].y + "px";
        }
    }

    function selectConversation(id) {
        activeConv = id;
        renderConversations();
        renderMainContent();
    }
    function deleteConversation(id) {
        const idx = conversations.findIndex(c=>c.id===id);
        if(idx>-1) {
            conversations.splice(idx,1);
            let tabDiv = document.getElementById('tab-content-' + id);
            if (tabDiv) tabDiv.remove();
            if(activeConv===id && conversations.length>0) activeConv = conversations[0].id;
            else if(conversations.length===0) {activeConv=null;}
            renderConversations();
            renderMainContent();
        }
    }
    function addConversation() {
        convCounter++;
        conversations.push({
            id: convCounter,
            title: "Sekme "+convCounter,
            settingsOpen: false,
            aralik: "5g",
            sourceUrl: "",
            gapratio: 30,
            gapfactor: 2,
            fsr: { fsr1: NaN, fsr2: NaN, fsr3: NaN }
        });
        activeConv = convCounter;
        renderConversations();
        renderMainContent();
    }
    function fsrToColor(val) {
        if (isNaN(val)) return "#bbb";
        val = Math.max(0, Math.min(4095, val));
        let t = val / 4095, r, g, b;
        if (t < 0.33) { r = 255; g = Math.round(255 * (t/0.33)); b = 0; }
        else if (t < 0.66) { r = Math.round(255 * (1-(t-0.33)/0.33)); g = 255; b = 0; }
        else { r = 0; g = Math.round(255 * (1-(t-0.66)/0.34)); b = Math.round(255*((t-0.66)/0.34)); }
        return `rgb(${r},${g},${b})`;
    }
    function getStartDate(a) {
        let n = new Date(), v = parseInt(a), u = a.slice(-1);
        if (u==='h') n.setHours(n.getHours()-v);
        else if (u==='g') n.setDate(n.getDate()-v);
        else if (u==='a') n.setMonth(n.getMonth()-v);
        else if (u==='y') n.setFullYear(n.getFullYear()-v);
        return n;
    }
    async function fetchAndPlot(convId, isFullscreen) {
        const conv = conversations.find(c=>c.id===convId);
        if (!conv) return;
        conv.aralik = document.getElementById("aralik"+convId).value;
        conv.sourceUrl = document.getElementById("sourceUrlInput"+convId).value;
        conv.gapratio = Number(document.getElementById("gapratio"+convId).value);
        conv.gapfactor = Number(document.getElementById("gapfactor"+convId).value);
        document.getElementById("adSoyad"+convId).textContent = "-";
        document.getElementById("tcNo"+convId).textContent = "-";
        const plotHolder = document.getElementById("plot"+convId);
        let isSmall = window.innerWidth < 700;
        if (!conv.sourceUrl) {
            drawEmptyPlot("plot"+convId, isFullscreen, isSmall);
            updateBubbles(NaN,NaN,NaN,convId);
            return;
        }
        let dataArr;
        let adSoyad = "-", tcNo = "-";
        try {
            let response = await (await fetch(conv.sourceUrl)).json();
            dataArr = response.veriler || response;
            if(typeof response.adSoyad !== "undefined")
                adSoyad = response.adSoyad;
            if(typeof response.tcNo !== "undefined")
                tcNo = response.tcNo;
        } catch {
            drawEmptyPlot("plot"+convId, isFullscreen, isSmall);
            return;
        }
        document.getElementById("adSoyad"+convId).textContent = adSoyad;
        document.getElementById("tcNo"+convId).textContent = tcNo;
        dataArr.sort((a, b) => new Date(a.zaman) - new Date(b.zaman));
        let filtered = dataArr.filter(item => {
            let z = new Date(item.zaman); return !isNaN(z.getTime()) && z >= getStartDate(conv.aralik);
        });
        if (filtered.length === 0) {
            drawEmptyPlot("plot"+convId, isFullscreen, isSmall);
            updateBubbles(NaN,NaN,NaN,convId);
            return;
        }
        const zaman = [], basma = [], hovertext = [], zamanTick = [];
        let lastFSR = null;
        for (const item of filtered) {
            let z = new Date(item.zaman);
            zaman.push(z);
            basma.push(Number(item.basmaDurumu));
            let lbl = z.toLocaleString('tr-TR').replace(", ", " ");
            hovertext.push('Zaman: '+lbl+'<br>Basma Durumu: '+item.basmaDurumu);
            zamanTick.push(z);
            lastFSR = item;
        }
        if (lastFSR && typeof lastFSR.fsr1 !== "undefined" && typeof lastFSR.fsr2 !== "undefined" && typeof lastFSR.fsr3 !== "undefined") {
            updateBubbles(lastFSR.fsr1, lastFSR.fsr2, lastFSR.fsr3, convId);
        } else {
            updateBubbles(NaN, NaN, NaN, convId);
        }
        let tickvals = [];
        let ticktext = [];
        let start = zaman[0].getTime(), end = zaman[zaman.length-1].getTime();
        let tickInt = Math.max(5, Math.floor((end-start)/(1000*60*6)));
        let curr = new Date(start - (start % (tickInt*60*1000)));
        while (curr.getTime() <= end) {
            tickvals.push(new Date(curr));
            if(isFullscreen) {
                let s = curr.getHours().toString().padStart(2,"0")+":"+curr.getMinutes().toString().padStart(2,"0");
                ticktext.push(s);
            } else {
                ticktext.push("");
            }
            curr = new Date(curr.getTime() + tickInt*60*1000);
        }
        let layout = {
            xaxis: {
                title: '',
                showticklabels: isFullscreen,
                zeroline: false,
                showgrid: false,
                gridcolor: '#e0ecf7',
                tickvals: tickvals,
                ticktext: ticktext,
                type: "date",
                automargin: true
            },
            yaxis: {
                title:{text:'Basma Durumu',font:{size:15,color:'#1976d2'}},
                tickfont:{size:13},
                showgrid:true,
                gridcolor: '#e0ecf7',
                tickvals:[1,2,3],
                range:[0.8,3.2]
            },
            hovermode:"closest",
            margin:{l:60,r:20,t:15,b:45},
            plot_bgcolor:'#fff',
            paper_bgcolor:'#fff',
            font:{ family:'Roboto, Arial, sans-serif'}
        };
        let trace = {
            x: zaman, y: basma, mode:'lines+markers',
            marker:{size:8,color:'red',line:{color:'#fff',width:1.3}},
            line:{shape:'linear',color:'red',width:2},
            text:hovertext, hoverinfo:'text'
        };
        Plotly.newPlot('plot'+convId, [trace], layout, {
            responsive: true,
            displayModeBar: false,
            displaylogo: false
        });
    }
    function drawEmptyPlot(plotId, isFullscreen, isSmall) {
        let ticktext = isFullscreen
            ? ["22:10", "22:15", "22:20", "22:25", "22:30"]
            : ["", "", "", "", ""];
        let layout = {
            xaxis: {
                title: '',
                showticklabels: isFullscreen,
                zeroline: false,
                showgrid: false,
                gridcolor: '#e0ecf7',
                tickvals: [0, 0.25, 0.5, 0.75, 1],
                ticktext: ticktext
            },
            yaxis: {
                title: {text:'Basma Durumu',font:{size:15,color:'#1976d2'}},
                tickfont: {size:13},
                showgrid: true,
                gridcolor: '#e0ecf7',
                tickvals: [1,2,3],
                range: [0.8, 3.2]
            },
            hovermode:"closest",
            margin:{l:60,r:20,t:15,b:45},
            plot_bgcolor:'#fff',
            paper_bgcolor:'#fff',
            font:{ family:'Roboto, Arial, sans-serif'}
        };
        Plotly.newPlot(plotId, [], layout, {
            responsive: true, displayModeBar:false, displaylogo:false
        });
    }
    function updateBubbles(fsr1, fsr2, fsr3, convId) {
        let b1 = document.getElementById("bubble-fsr1-"+convId),
            b2 = document.getElementById("bubble-fsr2-"+convId),
            b3 = document.getElementById("bubble-fsr3-"+convId);
        if (b1) b1.style.background = fsrToColor(fsr1);
        if (b2) b2.style.background = fsrToColor(fsr2);
        if (b3) b3.style.background = fsrToColor(fsr3);
    }
    renderConversations();
    renderMainContent();
    </script>
</body>
</html>
