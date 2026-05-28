<!DOCTYPE html>
<html lang="fr" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>HandBet Analytics | Live Odds & Trends</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css" rel="stylesheet">
    <link href="https://fonts.googleapis.com/css2?family=Roboto+Mono:wght@400;500;600;700&family=Inter:wght@400;500;600;700;800&display=swap" rel="stylesheet">
    <script>
        tailwind.config = {
            darkMode: 'class',
            theme: {
                extend: {
                    fontFamily: {
                        sans: ['Inter', 'sans-serif'],
                        mono: ['Roboto Mono', 'monospace'], // Pour les cotes et les chiffres
                    },
                    colors: {
                        dark: {
                            900: '#0a0f1c', // Fond principal très sombre
                            800: '#111827', // Panneaux
                            700: '#1f2937', // Bordures et hover
                        },
                        neon: {
                            green: '#00ff88',
                            red: '#ff3366',
                            blue: '#00ccff',
                            orange: '#ffaa00'
                        }
                    }
                }
            }
        }
    </script>
    <style>
        body { background-color: #0a0f1c; color: #f3f4f6; }
        
        /* Animations de fluctuation des cotes */
        @keyframes flash-up {
            0% { background-color: rgba(0, 255, 136, 0.4); color: #fff; transform: scale(1.05); }
            100% { background-color: transparent; color: inherit; transform: scale(1); }
        }
        @keyframes flash-down {
            0% { background-color: rgba(255, 51, 102, 0.4); color: #fff; transform: scale(1.05); }
            100% { background-color: transparent; color: inherit; transform: scale(1); }
        }
        
        .odd-up { animation: flash-up 1.5s ease-out; color: #00ff88 !important; }
        .odd-down { animation: flash-down 1.5s ease-out; color: #ff3366 !important; }
        
        /* Barres de tendance */
        .trend-bar { transition: width 0.5s ease-in-out; }
        
        /* Ticker pour les infos en direct */
        .news-ticker { white-space: nowrap; animation: ticker 25s linear infinite; }
        @keyframes ticker {
            0% { transform: translateX(100%); }
            100% { transform: translateX(-100%); }
        }

        /* Scrollbar furtive */
        ::-webkit-scrollbar { width: 6px; height: 6px; }
        ::-webkit-scrollbar-track { background: #0a0f1c; }
        ::-webkit-scrollbar-thumb { background: #1f2937; border-radius: 3px; }
        ::-webkit-scrollbar-thumb:hover { background: #374151; }
    </style>
</head>
<body class="antialiased min-h-screen flex flex-col font-sans">

    <!-- Navigation ultra-compacte type dashboard boursier -->
    <nav class="bg-dark-800 border-b border-dark-700 sticky top-0 z-50">
        <div class="max-w-[1400px] mx-auto px-4">
            <div class="flex items-center justify-between h-14">
                <div class="flex items-center gap-6">
                    <a href="#" class="flex items-center gap-2">
                        <i class="fa-solid fa-chart-line text-neon-blue text-xl"></i>
                        <span class="font-bold text-lg tracking-wider text-white">HANDBET<span class="text-neon-blue font-light">ANALYTICS</span></span>
                    </a>
                    <div class="hidden md:flex space-x-1">
                        <a href="#" class="px-3 py-1.5 rounded-md bg-dark-700 text-white text-sm font-medium flex items-center gap-2">
                            <i class="fa-solid fa-bolt text-neon-orange"></i> Live Scanner
                        </a>
                        <a href="#" class="px-3 py-1.5 rounded-md text-gray-400 hover:text-white hover:bg-dark-700 text-sm font-medium transition-colors">
                            <i class="fa-solid fa-hospital-user"></i> Blessures
                        </a>
                        <a href="#" class="px-3 py-1.5 rounded-md text-gray-400 hover:text-white hover:bg-dark-700 text-sm font-medium transition-colors">
                            <i class="fa-solid fa-money-bill-trend-up"></i> Dropping Odds
                        </a>
                    </div>
                </div>
                <div class="flex items-center gap-4">
                    <div class="hidden lg:flex items-center gap-2 text-xs font-mono text-gray-400 bg-dark-900 px-3 py-1.5 rounded border border-dark-700">
                        <span class="w-2 h-2 rounded-full bg-neon-green animate-pulse"></span>
                        Flux API Actif : <span id="last-update-time">10:42:05</span>
                    </div>
                    <button class="bg-neon-blue hover:bg-blue-400 text-dark-900 px-4 py-1.5 rounded font-bold text-sm transition-colors shadow-[0_0_10px_rgba(0,204,255,0.3)]">
                        Mon Compte
                    </button>
                </div>
            </div>
        </div>
    </nav>

    <!-- News Ticker (Urgent infos, Blessures) -->
    <div class="bg-dark-900 border-b border-dark-700 overflow-hidden relative h-8 flex items-center">
        <div class="absolute left-0 top-0 bottom-0 z-10 bg-gradient-to-r from-dark-900 to-transparent w-24 flex items-center px-4">
            <span class="text-xs font-bold text-neon-red uppercase flex items-center gap-1">
                <i class="fa-solid fa-bell"></i> INFO
            </span>
        </div>
        <div class="news-ticker text-xs font-mono text-gray-300 w-full pl-24">
            <span class="mx-4"><span class="text-neon-orange font-bold">INFO BLESSURE:</span> Dika Mem (Barça) incertain pour le match de ce soir (Épaule).</span> • 
            <span class="mx-4"><span class="text-neon-blue font-bold">SMART MONEY:</span> Chute de la cote de Montpellier de 2.10 à 1.85 face à Nantes.</span> • 
            <span class="mx-4"><span class="text-neon-red font-bold">ALERTE ABSENCE:</span> Andreas Wolff (Kiel) forfait de dernière minute.</span>
        </div>
    </div>

    <main class="flex-grow max-w-[1400px] mx-auto w-full px-4 py-6">
        <div class="grid grid-cols-1 lg:grid-cols-4 gap-6">
            
            <!-- Colonne de gauche : Matchs et Cotes (Prend 3 colonnes) -->
            <div class="lg:col-span-3 space-y-4">
                
                <div class="flex justify-between items-end mb-4">
                    <div>
                        <h1 class="text-2xl font-bold text-white flex items-center gap-2">
                            <i class="fa-solid fa-radar text-neon-green"></i> Live Odds Scanner
                        </h1>
                        <p class="text-gray-400 text-sm mt-1">Mise à jour automatique des cotes et volumes de paris.</p>
                    </div>
                    <div class="flex gap-2">
                        <select class="bg-dark-800 border border-dark-700 text-white text-sm rounded-md px-3 py-1.5 outline-none focus:border-neon-blue font-mono">
                            <option>Ligue des Champions</option>
                            <option>Starligue</option>
                            <option>Bundesliga</option>
                        </select>
                    </div>
                </div>

                <!-- Conteneur dynamique des matchs -->
                <div id="analytics-matches-container" class="space-y-4">
                    <!-- Les matchs seront générés par JS ici -->
                </div>

            </div>

            <!-- Colonne de droite : Insights et Outils -->
            <div class="space-y-6">
                
                <!-- Widget : Radar Infirmerie -->
                <div class="bg-dark-800 rounded-lg border border-dark-700 overflow-hidden">
                    <div class="bg-dark-700/50 p-3 border-b border-dark-700 flex justify-between items-center">
                        <h3 class="font-bold text-white text-sm flex items-center gap-2">
                            <i class="fa-solid fa-kit-medical text-neon-red"></i> Radar Blessures Majeures
                        </h3>
                    </div>
                    <div class="p-4 space-y-4">
                        <!-- Blessure 1 -->
                        <div class="flex items-start gap-3">
                            <div class="w-8 h-8 rounded bg-red-900/30 text-neon-red flex items-center justify-center flex-shrink-0 border border-red-900/50">
                                <i class="fa-solid fa-crutch"></i>
                            </div>
                            <div>
                                <div class="text-sm font-bold text-white">Andreas Wolff <span class="text-xs font-normal text-gray-400">(THW Kiel)</span></div>
                                <div class="text-xs text-neon-red font-medium mt-0.5">Forfait Confirmé (Déchirure)</div>
                                <div class="text-xs text-gray-400 mt-1"><i class="fa-solid fa-arrow-trend-down"></i> Impact: Grosse baisse probabilité victoire Kiel.</div>
                            </div>
                        </div>
                        <!-- Blessure 2 -->
                        <div class="flex items-start gap-3">
                            <div class="w-8 h-8 rounded bg-orange-900/30 text-neon-orange flex items-center justify-center flex-shrink-0 border border-orange-900/50">
                                <i class="fa-solid fa-triangle-exclamation"></i>
                            </div>
                            <div>
                                <div class="text-sm font-bold text-white">Dika Mem <span class="text-xs font-normal text-gray-400">(FC Barcelone)</span></div>
                                <div class="text-xs text-neon-orange font-medium mt-0.5">Incertain (Test à l'échauffement)</div>
                                <div class="text-xs text-gray-400 mt-1"><i class="fa-solid fa-scale-unbalanced"></i> Impact: Les bookmakers bloquent les cotes.</div>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Widget : Value Bets Détectés (Algorithme) -->
                <div class="bg-dark-800 rounded-lg border border-dark-700 overflow-hidden relative">
                    <div class="absolute top-0 right-0 p-2">
                        <span class="flex h-3 w-3 relative">
                          <span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-neon-blue opacity-75"></span>
                          <span class="relative inline-flex rounded-full h-3 w-3 bg-neon-blue"></span>
                        </span>
                    </div>
                    <div class="bg-dark-700/50 p-3 border-b border-dark-700">
                        <h3 class="font-bold text-white text-sm flex items-center gap-2">
                            <i class="fa-solid fa-microchip text-neon-blue"></i> IA Value Bets
                        </h3>
                    </div>
                    <div class="p-4">
                        <div class="bg-dark-900 border border-neon-blue/30 rounded p-3 mb-3">
                            <div class="flex justify-between items-center mb-2">
                                <span class="text-xs text-gray-400">Montpellier - Nantes</span>
                                <span class="px-2 py-0.5 bg-neon-blue/20 text-neon-blue text-[10px] font-bold rounded uppercase">Recommandé</span>
                            </div>
                            <div class="font-bold text-white text-sm mb-1">Victoire Montpellier</div>
                            <div class="flex justify-between items-end">
                                <div>
                                    <span class="text-xs text-gray-500 line-through">Cote Initiale: 2.10</span><br>
                                    <span class="text-sm font-mono font-bold text-neon-green">Cote Actuelle: 1.85</span>
                                </div>
                                <button class="bg-dark-700 hover:bg-neon-blue hover:text-dark-900 border border-dark-600 text-white px-3 py-1 rounded text-xs font-bold transition-colors">
                                    Parier
                                </button>
                            </div>
                        </div>
                        <p class="text-[10px] text-gray-500 leading-tight text-center">L'algorithme détecte une anomalie de marché due à l'absence du pivot titulaire adverse annoncée il y a 10 minutes.</p>
                    </div>
                </div>

            </div>
        </div>
    </main>

    <script>
        /**
         * DONNÉES SIMULÉES
         * Dans un vrai projet, ces données viendraient d'une API (ex: Sportmonks) via fetch() ou WebSockets.
         */
        const matchesState = [
            {
                id: 1,
                status: 'live',
                time: "48:12",
                league: "LDC",
                home: { name: 'Paris SG', score: 24, missing: [] },
                away: { name: 'THW Kiel', score: 22, missing: ['A. Wolff (G)'] },
                odds: { 1: 1.15, X: 14.00, 2: 7.50 },
                volume: { 1: 82, X: 2, 2: 16 } // Pourcentage des mises
            },
            {
                id: 2,
                status: 'upcoming',
                time: "20:45",
                league: "LDC",
                home: { name: 'FC Barcelone', score: null, missing: ['D. Mem (?)'] },
                away: { name: 'Veszprém', score: null, missing: [] },
                odds: { 1: 1.65, X: 8.50, 2: 2.75 },
                volume: { 1: 45, X: 10, 2: 45 }
            },
            {
                id: 3,
                status: 'live',
                time: "12:05",
                league: "Starligue",
                home: { name: 'Montpellier HB', score: 6, missing: [] },
                away: { name: 'HBC Nantes', score: 6, missing: [] },
                odds: { 1: 1.85, X: 7.50, 2: 2.15 },
                volume: { 1: 65, X: 5, 2: 30 }
            }
        ];

        // Mémoriser les anciennes cotes pour savoir si ça monte ou descend
        let previousOdds = JSON.parse(JSON.stringify(matchesState.map(m => m.odds)));

        // Fonction pour formater le statut et les blessures
        function renderTeam(teamData) {
            let html = `<span class="font-bold text-white text-base">${teamData.name}</span>`;
            if (teamData.missing.length > 0) {
                html += `<span class="ml-2 inline-flex items-center justify-center w-5 h-5 rounded bg-red-900/40 border border-red-500/50 text-neon-red text-[10px]" title="Blessure: ${teamData.missing.join(', ')}">
                            <i class="fa-solid fa-briefcase-medical"></i>
                         </span>`;
            }
            return html;
        }

        // Fonction pour générer la barre de tendance des mises (Money Volume)
        function renderVolumeBar(volume) {
            return `
                <div class="mt-3">
                    <div class="flex justify-between text-[10px] text-gray-400 mb-1 font-mono uppercase">
                        <span>Distribution des mises (Smart Money)</span>
                    </div>
                    <div class="w-full h-1.5 bg-dark-900 rounded-full flex overflow-hidden border border-dark-700">
                        <div class="bg-neon-blue h-full trend-bar" style="width: ${volume['1']}%" title="Victoire Domicile: ${volume['1']}%"></div>
                        <div class="bg-gray-500 h-full trend-bar" style="width: ${volume['X']}%" title="Nul: ${volume['X']}%"></div>
                        <div class="bg-neon-orange h-full trend-bar" style="width: ${volume['2']}%" title="Victoire Extérieur: ${volume['2']}%"></div>
                    </div>
                    <div class="flex justify-between text-[10px] font-mono mt-1">
                        <span class="text-neon-blue">${volume['1']}% (1)</span>
                        <span class="text-gray-500">${volume['X']}% (N)</span>
                        <span class="text-neon-orange">${volume['2']}% (2)</span>
                    </div>
                </div>
            `;
        }

        // Rendu principal de l'interface
        function renderMatches() {
            const container = document.getElementById('analytics-matches-container');
            container.innerHTML = ''; // Nettoyer

            matchesState.forEach((match, index) => {
                const prevO = previousOdds[index];
                
                // Déterminer les classes CSS pour les flashs de cotes
                const getClassForOdd = (type) => {
                    const current = match.odds[type];
                    const prev = prevO[type];
                    let animClass = '';
                    if (current > prev) animClass = 'odd-up';
                    else if (current < prev) animClass = 'odd-down';
                    
                    return `col-span-1 py-2 px-1 rounded bg-dark-900 border border-dark-600 flex flex-col items-center justify-center cursor-pointer hover:border-neon-blue transition-colors font-mono relative ${animClass}`;
                };

                const getTrendArrow = (type) => {
                    const current = match.odds[type];
                    const prev = prevO[type];
                    if (current > prev) return `<i class="fa-solid fa-caret-up text-[10px] text-neon-green absolute top-1 right-1"></i>`;
                    if (current < prev) return `<i class="fa-solid fa-caret-down text-[10px] text-neon-red absolute top-1 right-1"></i>`;
                    return '';
                };

                let timeDisplay = match.status === 'live' 
                    ? `<span class="text-neon-green animate-pulse font-mono mr-2">●</span><span class="text-neon-green font-mono">${match.time}</span>`
                    : `<span class="text-gray-400 font-mono"><i class="fa-regular fa-clock"></i> ${match.time}</span>`;

                let scoreDisplay = match.status === 'live'
                    ? `<div class="bg-dark-900 border border-dark-700 rounded px-3 py-1 font-mono text-xl font-bold text-white tracking-widest">${match.home.score} - ${match.away.score}</div>`
                    : `<div class="text-gray-500 font-mono text-sm px-3 py-1 border border-dark-700 border-dashed rounded">VS</div>`;

                const cardHTML = `
                    <div class="bg-dark-800 rounded-lg border border-dark-700 p-4 transition-all">
                        <div class="flex flex-col md:flex-row justify-between gap-4">
                            
                            <!-- Infos Equipes & Score -->
                            <div class="flex-grow flex flex-col justify-center">
                                <div class="text-[10px] uppercase tracking-wider text-gray-500 mb-2 font-bold">${match.league} | ${timeDisplay}</div>
                                <div class="flex items-center justify-between pr-0 md:pr-6">
                                    <div class="flex flex-col gap-3 flex-1">
                                        <div class="flex items-center justify-between">
                                            <div>${renderTeam(match.home)}</div>
                                        </div>
                                        <div class="flex items-center justify-between">
                                            <div>${renderTeam(match.away)}</div>
                                        </div>
                                    </div>
                                    <div class="ml-4 flex-shrink-0 text-center">
                                        ${scoreDisplay}
                                    </div>
                                </div>
                            </div>

                            <!-- Bloc Cotes & Volumes -->
                            <div class="w-full md:w-64 flex-shrink-0 flex flex-col justify-center border-t md:border-t-0 md:border-l border-dark-700 pt-4 md:pt-0 md:pl-4">
                                <div class="text-[10px] text-gray-400 mb-2 uppercase font-mono tracking-wider flex justify-between">
                                    <span>Cotes en direct</span>
                                    <span class="text-neon-blue"><i class="fa-solid fa-rotate"></i> Auto</span>
                                </div>
                                <div class="grid grid-cols-3 gap-2">
                                    <div class="${getClassForOdd('1')}" id="odd-${match.id}-1">
                                        ${getTrendArrow('1')}
                                        <span class="text-[10px] text-gray-500 mb-0.5">1</span>
                                        <span class="text-sm font-bold text-white">${match.odds['1'].toFixed(2)}</span>
                                    </div>
                                    <div class="${getClassForOdd('X')}" id="odd-${match.id}-X">
                                        ${getTrendArrow('X')}
                                        <span class="text-[10px] text-gray-500 mb-0.5">N</span>
                                        <span class="text-sm font-bold text-white">${match.odds['X'].toFixed(2)}</span>
                                    </div>
                                    <div class="${getClassForOdd('2')}" id="odd-${match.id}-2">
                                        ${getTrendArrow('2')}
                                        <span class="text-[10px] text-gray-500 mb-0.5">2</span>
                                        <span class="text-sm font-bold text-white">${match.odds['2'].toFixed(2)}</span>
                                    </div>
                                </div>
                                
                                ${renderVolumeBar(match.volume)}
                            </div>
                        </div>
                    </div>
                `;
                container.insertAdjacentHTML('beforeend', cardHTML);
            });
        }

        // Fonction pour simuler la réception de nouvelles données de l'API (Fluctuation de cotes et score)
        function simulateLiveUpdates() {
            // Sauvegarder l'état précédent
            previousOdds = JSON.parse(JSON.stringify(matchesState.map(m => m.odds)));

            let hasChanges = false;

            matchesState.forEach(match => {
                // Simuler un changement de score pour les matchs en direct (10% de chance toutes les 3s)
                if (match.status === 'live' && Math.random() > 0.9) {
                    if (Math.random() > 0.5) match.home.score++;
                    else match.away.score++;
                    
                    // Avancer le temps simulé
                    let [min, sec] = match.time.split(':').map(Number);
                    sec += 15;
                    if(sec >= 60) { min++; sec -= 60; }
                    match.time = `${min}:${sec < 10 ? '0'+sec : sec}`;
                    hasChanges = true;
                }

                // Simuler une fluctuation des cotes (30% de chance)
                if (Math.random() > 0.7) {
                    // Choisir une issue au hasard (1, X, ou 2)
                    const types = ['1', 'X', '2'];
                    const typeToChange = types[Math.floor(Math.random() * types.length)];
                    
                    // Changement entre -0.05 et +0.05
                    const change = (Math.random() * 0.1) - 0.05;
                    
                    // Appliquer et empêcher les cotes < 1.01
                    match.odds[typeToChange] = Math.max(1.01, match.odds[typeToChange] + change);
                    
                    // Simuler un changement de volume d'argent lié à la cote
                    if(change < 0) {
                        // Si la cote baisse, c'est que l'argent va dessus
                        match.volume[typeToChange] = Math.min(90, match.volume[typeToChange] + 2);
                    } else {
                        match.volume[typeToChange] = Math.max(5, match.volume[typeToChange] - 2);
                    }
                    
                    hasChanges = true;
                }
            });

            if (hasChanges) {
                renderMatches();
                // Mettre à jour l'horloge API
                const now = new Date();
                document.getElementById('last-update-time').innerText = now.toLocaleTimeString('fr-FR', { hour12: false });
            }
        }

        // Initialisation
        document.addEventListener('DOMContentLoaded', () => {
            renderMatches();
            
            // Simuler la connexion WebSockets / Polling d'API
            // L'algorithme tourne toutes les 3 secondes pour vérifier s'il y a des changements
            setInterval(simulateLiveUpdates, 3000);
            
            // Mettre à jour l'horloge statique une première fois
            document.getElementById('last-update-time').innerText = new Date().toLocaleTimeString('fr-FR', { hour12: false });
        });
    </script>
</body>
</html>
