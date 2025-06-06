<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Placar dos Jogos SENAC</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700;900&display=swap" rel="stylesheet">
    <style>
        /* Estilos gerais do corpo e container principal */
        body {
            font-family: 'Inter', sans-serif;
            background-color: #F0F4F8; /* Azul-cinzento claro, quase branco */
            display: flex;
            justify-content: center;
            align-items: flex-start; /* Alinhar ao topo para melhor rolagem em telas menores */
            min-height: 100vh;
            padding: 20px;
            box-sizing: border-box;
        }
        .scoreboard-container {
            background-color: #ffffff;
            border-radius: 16px;
            box-shadow: 0 10px 25px rgba(0, 0, 0, 0.1);
            padding: 24px;
            max-width: 1200px;
            width: 100%;
            overflow-x: auto; /* Habilitar rolagem horizontal para telas pequenas */
        }

        /* Estilos da grade do placar (cabeçalho e linhas) */
        .grid-header, .grid-row {
            display: grid;
            gap: 8px; /* Espaçamento entre as células da grade */
            padding: 10px 0;
            border-bottom: 1px solid #E2E8F0; /* Linha divisória para as linhas */
            align-items: center;
        }
        .grid-header {
            font-weight: 700;
            background-color: #F8FAFC; /* Fundo mais claro para o cabeçalho */
            border-radius: 8px 8px 0 0;
            position: sticky; /* Tornar o cabeçalho fixo ao rolar */
            top: 0;
            z-index: 10; /* Garantir que fique acima de outros conteúdos */
        }
        .grid-cell {
            padding: 8px;
            text-align: center;
            white-space: nowrap; /* Evitar que o texto quebre a linha */
            overflow: hidden;
            text-overflow: ellipsis; /* Adicionar reticências para texto que transborda */
        }
        .header-orange-text {
            color: #F97316; /* Laranja Tailwind-500 */
        }

        /* Estilos dos inputs de equipe e pontuação */
        .team-name-input {
            width: 100%;
            padding: 4px;
            border: 1px solid #CBD5E0;
            border-radius: 4px;
            text-align: center;
            font-weight: 600;
            color: #333;
        }
        .score-input {
            width: 100%;
            padding: 4px;
            border: 1px solid #CBD5E0;
            border-radius: 4px;
            text-align: center;
            font-weight: 600;
            color: #007BFF; /* Uma cor distinta para as pontuações */
        }
        .total-score {
            font-weight: 700;
            color: #10B981; /* Verde Tailwind-500 */
            font-size: 1.1em;
        }

        /* Estilos dos botões de ação na linha da equipe */
        .action-buttons {
            display: flex;
            gap: 4px;
            justify-content: center;
        }
        .action-buttons button {
            padding: 6px 10px;
            border-radius: 6px;
            font-size: 0.85em;
            cursor: pointer;
            transition: background-color 0.2s ease;
        }
        .edit-team-btn {
            background-color: #3B82F6; /* Azul Tailwind-500 */
            color: white;
        }
        .edit-team-btn:hover:not(:disabled) {
            background-color: #2563EB; /* Azul Tailwind-600 */
        }
        .remove-team-btn {
            background-color: #EF4444; /* Vermelho Tailwind-500 */
            color: white;
        }
        .remove-team-btn:hover:not(:disabled) {
            background-color: #DC2626; /* Vermelho Tailwind-600 */
        }

        /* Estilos dos botões principais (adicionar equipe, reiniciar) */
        .add-team-btn {
            background-color: #10B981; /* Verde Tailwind-500 */
            color: white;
            padding: 12px 20px;
            font-size: 1rem;
            border-radius: 8px;
            transition: background-color 0.2s ease;
        }
        .add-team-btn:hover:not(:disabled) {
            background-color: #059669; /* Verde Tailwind-600 */
        }
        .reset-btn {
            background-color: #F59E0B; /* Âmbar Tailwind-500 */
            color: white;
            padding: 12px 20px;
            font-size: 1rem;
            border-radius: 8px;
            transition: background-color 0.2s ease;
        }
        .reset-btn:hover:not(:disabled) {
            background-color: #D97706; /* Âmbar Tailwind-600 */
        }

        /* Mensagens de estado e carregamento */
        .empty-state {
            text-align: center;
            padding: 40px;
            color: #64748B;
            font-style: italic;
        }
        .loading-indicator {
            text-align: center;
            padding: 40px;
            color: #64748B;
        }
        .status-message {
            position: fixed;
            top: 20px;
            right: 20px;
            background-color: #F97316; /* Laranja-500 */
            color: white;
            padding: 12px 20px;
            border-radius: 8px;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
            z-index: 1000;
            opacity: 0;
            transition: opacity 0.3s ease-in-out;
        }
        .status-message.error {
            background-color: #EF4444; /* Vermelho-500 */
        }
        .status-message.show {
            opacity: 1;
        }
        #userIdDisplay {
            text-align: right;
            font-size: 0.85em;
            color: #64748B;
            margin-bottom: 10px;
        }

        /* Estilos do modal de confirmação */
        .modal-overlay {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.6);
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 1000;
            opacity: 0;
            visibility: hidden;
            transition: opacity 0.3s ease, visibility 0.3s ease;
        }
        .modal-overlay.show {
            opacity: 1;
            visibility: visible;
        }
        .modal-content {
            background-color: white;
            padding: 30px;
            border-radius: 12px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.2);
            text-align: center;
            max-width: 400px;
            width: 90%;
            transform: translateY(-20px);
            opacity: 0;
            transition: transform 0.3s ease-out, opacity 0.3s ease-out;
        }
        .modal-overlay.show .modal-content {
            transform: translateY(0);
            opacity: 1;
        }
        .modal-buttons {
            margin-top: 25px;
            display: flex;
            justify-content: center;
            gap: 15px;
        }
        .modal-buttons button {
            padding: 10px 20px;
            border-radius: 8px;
            font-weight: 600;
            cursor: pointer;
            transition: background-color 0.2s ease;
        }
        #modalConfirmBtn {
            background-color: #EF4444; /* Vermelho-500 */
            color: white;
        }
        #modalConfirmBtn:hover {
            background-color: #DC2626; /* Vermelho-600 */
        }
        #modalCancelBtn {
            background-color: #CBD5E0; /* Cinzento-300 */
            color: #4A5568;
        }
        #modalCancelBtn:hover {
            background-color: #A0AEC0; /* Cinzento-400 */
        }
        #modalMessage {
            font-size: 1.1em;
            color: #333;
        }

        /* Estilos do Cronômetro de Jogo */
        .timer-container {
            background-color: #E0F2F7; /* Fundo azul claro */
            border-radius: 16px;
            box-shadow: 0 8px 20px rgba(0, 0, 0, 0.1);
            padding: 20px;
            margin-bottom: 25px;
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 10px;
            border: 2px solid #03A9F4; /* Borda azul claro */
        }
        #timerDisplay {
            font-size: 3.5rem; /* Tamanho da fonte maior */
            font-weight: 700;
            color: #2196F3; /* Azul-500 */
            letter-spacing: 2px;
            text-shadow: 2px 2px 4px rgba(0,0,0,0.1);
        }
        .timer-buttons {
            display: flex;
            gap: 10px;
        }
        .timer-buttons button {
            padding: 10px 20px;
            border-radius: 8px;
            font-weight: 600;
            color: white;
            cursor: pointer;
            transition: background-color 0.2s ease;
        }
        .timer-buttons button:disabled {
            opacity: 0.6;
            cursor: not-allowed;
        }
        .timer-buttons .bg-blue-600 { background-color: #2196F3; } /* Azul-500 */
        .timer-buttons .bg-blue-600:hover:not(:disabled) { background-color: #1976D2; } /* Azul-700 */
        .timer-buttons .bg-purple-600 { background-color: #9C27B0; } /* Roxo-500 */
        .timer-buttons .bg-purple-600:hover:not(:disabled) { background-color: #7B1FA2; } /* Roxo-700 */
        .timer-buttons .bg-red-600 { background-color: #F44336; } /* Vermelho-500 */
        .timer-buttons .bg-red-600:hover:not(:disabled) { background-color: #D32F2F; } /* Vermelho-700 */

        /* Estilos do Gerenciador de Modalidades */
        .modality-manager-container {
            background-color: #F0FDF4; /* Fundo verde claro */
            border-radius: 16px;
            box-shadow: 0 8px 20px rgba(0, 0, 0, 0.1);
            padding: 20px;
            margin-top: 25px; /* Margem superior para separar do timer */
            margin-bottom: 25px;
            border: 2px solid #4CAF50; /* Borda verde */
        }
        .modality-manager-container h2 {
            text-align: center;
            color: #22C55E; /* Verde Tailwind-500 */
            margin-bottom: 15px;
        }
        .modality-input-group {
            display: flex;
            gap: 10px;
            margin-bottom: 20px;
            justify-content: center;
        }
        .modality-input-group input {
            padding: 10px;
            border: 1px solid #CBD5E0;
            border-radius: 8px;
            flex-grow: 1;
            max-width: 300px;
        }
        .modality-input-group button {
            background-color: #22C55E; /* Verde Tailwind-500 */
            color: white;
            padding: 10px 20px;
            border-radius: 8px;
            font-weight: 600;
            cursor: pointer;
            transition: background-color 0.2s ease;
        }
        .modality-input-group button:hover {
            background-color: #16A34A; /* Verde Tailwind-600 */
        }
        #currentModalities {
            display: flex;
            flex-wrap: wrap;
            gap: 10px;
            justify-content: center;
            margin-top: 15px;
        }
        .modality-tag {
            background-color: #DBEAFE; /* Azul Tailwind-100 */
            color: #1E40AF; /* Azul Tailwind-800 */
            padding: 8px 12px;
            border-radius: 20px;
            font-size: 0.9em;
            font-weight: 600;
            display: flex;
            align-items: center;
            gap: 8px;
            transition: background-color 0.2s ease;
            position: relative;
        }
        .modality-tag:hover {
            background-color: #BFDBFE; /* Azul Tailwind-200 */
        }
        .modality-tag button {
            background: none;
            border: none;
            color: #1E40AF;
            font-size: 0.8em;
            cursor: pointer;
            padding: 0 4px;
            transition: color 0.2s ease;
        }
        .modality-tag button:hover {
            color: #EF4444; /* Vermelho para excluir */
        }
        .modality-tag .edit-modality-input {
            background: none;
            border: none;
            padding: 0;
            margin: 0;
            font-size: inherit;
            font-weight: inherit;
            color: inherit;
            text-align: center;
            outline: none;
            width: auto;
            min-width: 50px;
            max-width: 150px;
        }
        .modality-tag .edit-modality-input:focus {
            border-bottom: 1px dashed #1E40AF;
        }

        /* Estilos do Contador Regressivo (novo) */
        .countdown-timer-container {
            background-color: #FFFDE7; /* Fundo amarelo claro */
            border-radius: 16px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.15); /* Sombra mais proeminente */
            padding: 25px 20px;
            margin-bottom: 30px; /* Margem aumentada para separação */
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 15px;
            border: 2px solid #FFD700; /* Borda dourada */
        }
        #countdownDisplay {
            font-size: 5rem; /* Tamanho da fonte muito maior */
            font-weight: 900; /* Extra negrito */
            color: #D32F2F; /* Vermelho escuro para destaque */
            letter-spacing: 4px;
            text-shadow: 3px 3px 6px rgba(0,0,0,0.2); /* Sombra mais proeminente */
            margin-bottom: 15px;
            font-family: 'Inter', sans-serif; /* Garantir consistência da fonte */
        }
        .countdown-timer-container .timer-buttons button {
            padding: 14px 28px; /* Botões maiores */
            font-size: 1.2rem; /* Fonte maior para os botões */
            border-radius: 12px;
        }
        .countdown-timer-container .bg-blue-600 { background-color: #007BFF; } /* Azul mais brilhante */
        .countdown-timer-container .bg-blue-600:hover:not(:disabled) { background-color: #0056b3; }
        .countdown-timer-container .bg-purple-600 { background-color: #6F42C1; } /* Roxo profundo */
        .countdown-timer-container .bg-purple-600:hover:not(:disabled) { background-color: #5a359b; }
        .countdown-timer-container .bg-red-600 { background-color: #DC3545; } /* Vermelho brilhante */
        .countdown-timer-container .bg-red-600:hover:not(:disabled) { background-color: #bd2130; }

        /* Ajustes gerais de botões para consistência */
        button {
            transition: background-color 0.2s ease, transform 0.1s ease;
        }
        button:hover:not(:disabled) {
            transform: translateY(-2px);
        }
        button:disabled {
            opacity: 0.5;
            cursor: not-allowed;
        }

    </style>
</head>
<body>
    <div class="scoreboard-container">
        <h1 class="text-4xl font-bold text-center text-gray-800 mb-2">SENAC PAULISTA</h1>
        <h1 class="text-3xl font-bold text-center text-gray-700 mb-6">Placar dos Jogos</h1>

        <div id="userIdDisplay" class="text-right text-sm text-gray-500 mb-4"></div>
        <div id="statusMessage" class="status-message"></div>

        <!-- Seção do Cronômetro de Jogo (já existente) -->
        <div class="timer-container">
            <h2 class="text-2xl font-bold text-gray-700 mb-3">Cronômetro de Jogo</h2>
            <div id="timerDisplay" class="text-5xl font-bold text-blue-600 mb-4">00:00:00</div>
            <div class="timer-buttons">
                <button id="startTimerBtn" class="bg-blue-600 text-white">Iniciar</button>
                <button id="pauseTimerBtn" class="bg-purple-600 text-white" disabled>Pausar</button>
                <button id="resetTimerBtn" class="bg-red-600 text-white" disabled>Reiniciar</button>
            </div>
        </div>
        <!-- Fim da Seção do Cronômetro de Jogo -->

        <!-- Nova Seção do Contador Regressivo -->
        <div class="countdown-timer-container">
            <h2 class="text-2xl font-bold text-gray-700 mb-3">Contador Regressivo</h2>
            <div class="flex items-center justify-center gap-3 mb-4">
                <input type="number" id="countdownMinutesInput" placeholder="Minutos" class="p-2 border rounded-md w-24 text-center text-xl font-semibold" min="0" max="999">
                <span class="text-2xl font-bold text-gray-600">:</span>
                <input type="number" id="countdownSecondsInput" placeholder="Segundos" class="p-2 border rounded-md w-24 text-center text-xl font-semibold" min="0" max="59">
            </div>
            <div id="countdownDisplay" class="countdown-display">00:00</div>
            <div class="timer-buttons">
                <button id="startCountdownBtn" class="bg-blue-600 text-white" disabled>Iniciar</button>
                <button id="pauseCountdownBtn" class="bg-purple-600 text-white" disabled>Pausar</button>
                <button id="resetCountdownBtn" class="bg-red-600 text-white" disabled>Reiniciar</button>
            </div>
        </div>
        <!-- Fim da Nova Seção do Contador Regressivo -->

        <!-- Seção do Gerenciador de Modalidades -->
        <div class="modality-manager-container">
            <h2 class="text-2xl font-bold text-green-500 mb-3">Gerenciar Modalidades</h2>
            <div class="modality-input-group">
                <input type="text" id="newModalityName" placeholder="Nome da nova modalidade" class="p-2 border rounded-md">
                <button id="addModalityBtn">Adicionar Modalidade</button>
            </div>
            <div id="currentModalities" class="flex flex-wrap gap-2 justify-center">
                <!-- As tags de modalidade serão renderizadas aqui -->
            </div>
        </div>
        <!-- Fim da Seção do Gerenciador de Modalidades -->


        <h2 class="text-2xl font-bold text-center text-gray-700 mb-4 mt-8">Placar</h2>

        <div id="scoreboard-grid" class="overflow-x-auto">
            <div id="gridHeader" class="grid-header">
                <!-- O cabeçalho da grade será preenchido dinamicamente pelo JS -->
            </div>
            <div id="scoreboard-body">
                <div id="loadingIndicator" class="loading-indicator">Carregando placar...</div>
                <div id="emptyState" class="empty-state hidden">Nenhuma turma adicionada ainda. Adicione uma turma para começar!</div>
                <!-- As linhas das equipes serão preenchidas dinamicamente pelo JS -->
            </div>
        </div>

        <div class="flex justify-center gap-4 mt-8">
            <button id="addTeamBtn" class="add-team-btn">Adicionar Turma</button>
            <button id="resetScoresBtn" class="reset-btn">Reiniciar Placar</button>
        </div>

        <!-- Modal de Confirmação -->
        <div id="confirmationModal" class="modal-overlay hidden">
            <div class="modal-content">
                <p id="modalMessage" class="text-lg text-gray-700 mb-6">Você tem certeza?</p>
                <div class="modal-buttons">
                    <button id="modalConfirmBtn" class="bg-red-500 text-white">Confirmar</button>
                    <button id="modalCancelBtn" class="bg-gray-300 text-gray-700">Cancelar</button>
                </div>
            </div>
        </div>

    </div>

    <script type="module">
        // Variáveis globais para os dados do placar
        let allTeams = [];
        // Modalidades pré-carregadas com IDs e ordem
        let allModalities = [
            { id: 'mod_limao', name: 'Corrida do limão', order: 1 },
            { id: 'mod_laranja', name: 'Dança da laranja', order: 2 },
            { id: 'mod_pescaria', name: 'Pescaria', order: 3 },
            { id: 'mod_argola', name: 'Jogo da argola', order: 4 },
            { id: 'mod_palhaco', name: 'Jogo do palhaço', order: 5 },
            { id: 'mod_rabo', name: 'Colocar o rabo no burro', order: 6 },
            { id: 'mod_maca', name: 'Pegar a maça', order: 7 }
        ];

        // Variáveis do Cronômetro de Jogo
        let timerInterval = null;
        let startTime = 0;
        let elapsedTime = 0;
        let isRunning = false;

        // Variáveis do Contador Regressivo
        let countdownInterval = null;
        let countdownRemainingTime = 0; // em milissegundos
        let isCountdownRunning = false;

        // Elementos DOM para o cronômetro existente
        const timerDisplay = document.getElementById('timerDisplay');
        const startTimerBtn = document.getElementById('startTimerBtn');
        const pauseTimerBtn = document.getElementById('pauseTimerBtn');
        const resetTimerBtn = document.getElementById('resetTimerBtn');

        // Elementos DOM para o novo contador regressivo
        const countdownMinutesInput = document.getElementById('countdownMinutesInput');
        const countdownSecondsInput = document.getElementById('countdownSecondsInput');
        const countdownDisplay = document.getElementById('countdownDisplay');
        const startCountdownBtn = document.getElementById('startCountdownBtn');
        const pauseCountdownBtn = document.getElementById('pauseCountdownBtn');
        const resetCountdownBtn = document.getElementById('resetCountdownBtn');

        // Elementos DOM para o placar
        const scoreboardBody = document.getElementById('scoreboard-body');
        const gridHeader = document.getElementById('gridHeader');
        const addTeamBtn = document.getElementById('addTeamBtn');
        const resetScoresBtn = document.getElementById('resetScoresBtn');
        const loadingIndicator = document.getElementById('loadingIndicator');
        const emptyStateMessage = document.getElementById('emptyState');
        const userIdDisplay = document.getElementById('userIdDisplay');
        const statusMessageDiv = document.getElementById('statusMessage');

        // Elementos para gerenciamento de modalidades
        const newModalityNameInput = document.getElementById('newModalityName');
        const addModalityBtn = document.getElementById('addModalityBtn');
        const currentModalitiesContainer = document.getElementById('currentModalities');

        // Elementos do modal
        const confirmationModal = document.getElementById('confirmationModal');
        const modalMessage = document.getElementById('modalMessage');
        const modalConfirmBtn = document.getElementById('modalConfirmBtn');
        const modalCancelBtn = document.getElementById('modalCancelBtn');
        let onConfirmCallback = null; // Callback a ser executado na confirmação do modal

        // --- Funções Auxiliares ---
        function generateUniqueId() {
            return '_' + Math.random().toString(36).substr(2, 9);
        }

        function showStatusMessage(message, type = 'status') {
            statusMessageDiv.textContent = message;
            statusMessageDiv.className = `status-message show ${type}`;
            setTimeout(() => {
                statusMessageDiv.classList.remove('show');
            }, 3000);
        }

        function showConfirmationModal(message, onConfirm) {
            modalMessage.textContent = message;
            onConfirmCallback = onConfirm;
            confirmationModal.classList.add('show');
        }

        // --- Persistência (LocalStorage) ---
        function saveData() {
            try {
                localStorage.setItem('allTeams', JSON.stringify(allTeams));
                localStorage.setItem('allModalities', JSON.stringify(allModalities));
                localStorage.setItem('timerElapsedTime', elapsedTime.toString());
                localStorage.setItem('timerIsRunning', isRunning.toString());
                localStorage.setItem('timerStartTime', startTime.toString());
                // Salvar estado do contador regressivo
                localStorage.setItem('countdownRemainingTime', countdownRemainingTime.toString());
                localStorage.setItem('isCountdownRunning', isCountdownRunning.toString());
                userIdDisplay.textContent = 'Dados salvos localmente no navegador'; // Atualizar mensagem
            } catch (e) {
                console.error("Erro ao salvar dados no localStorage:", e);
                showStatusMessage("Erro ao salvar dados localmente. Verifique o espaço de armazenamento do navegador.", 'error');
            }
        }

        function loadData() {
            try {
                const storedTeams = localStorage.getItem('allTeams');
                const storedModalities = localStorage.getItem('allModalities');
                const storedElapsedTime = localStorage.getItem('timerElapsedTime');
                const storedIsRunning = localStorage.getItem('timerIsRunning');
                const storedStartTime = localStorage.getItem('timerStartTime');
                // Carregar estado do contador regressivo
                const storedCountdownRemainingTime = localStorage.getItem('countdownRemainingTime');
                const storedIsCountdownRunning = localStorage.getItem('isCountdownRunning');


                if (storedTeams) {
                    allTeams = JSON.parse(storedTeams);
                } else {
                    allTeams = [];
                }

                if (storedModalities) {
                    // Carregar modalidades do localStorage e garantir que as novas modalidades padrão não sejam removidas
                    const loadedModalities = JSON.parse(storedModalities);
                    // Criar um mapa das modalidades padrão para fácil acesso
                    const defaultModalityMap = new Map(allModalities.map(m => [m.id, m]));

                    // Filtrar modalidades carregadas para remover duplicatas e garantir que as padrão existam
                    const uniqueLoadedModalities = [];
                    const seenModalityIds = new Set();

                    // Adicionar modalidades carregadas que não são padrão ou que são padrão mas foram modificadas
                    loadedModalities.forEach(m => {
                        if (!defaultModalityMap.has(m.id) || defaultModalityMap.get(m.id).name !== m.name || defaultModalityMap.get(m.id).order !== m.order) {
                            if (!seenModalityIds.has(m.id)) {
                                uniqueLoadedModalities.push(m);
                                seenModalityIds.add(m.id);
                            }
                        }
                    });

                    // Adicionar modalidades padrão que ainda não foram adicionadas
                    allModalities.forEach(m => {
                        if (!seenModalityIds.has(m.id)) {
                            uniqueLoadedModalities.push(m);
                            seenModalityIds.add(m.id);
                        }
                    });
                    
                    allModalities = uniqueLoadedModalities;

                    allModalities.sort((a, b) => a.order - b.order);
                } else {
                    // Se não houver modalidades armazenadas, usa as padrão definidas acima
                    allModalities.sort((a, b) => a.order - b.order);
                }
                
                // Garantir que todas as equipes tenham pontuações para todas as modalidades atuais
                allTeams.forEach(team => {
                    allModalities.forEach(modality => {
                        if (team.scores[modality.id] === undefined) {
                            team.scores[modality.id] = 0;
                        }
                    });
                    // Remover pontuações de modalidades que não existem mais
                    for (const scoreId in team.scores) {
                        if (!allModalities.some(mod => mod.id === scoreId)) {
                            delete team.scores[scoreId];
                        }
                    }
                    team.total = calculateTotal(team); // Recalcular o total após ajustes
                });


                if (storedElapsedTime) {
                    elapsedTime = parseInt(storedElapsedTime, 10);
                }
                if (storedIsRunning === 'true') {
                    isRunning = true;
                } else {
                    isRunning = false;
                }
                if (storedStartTime) {
                    startTime = parseInt(storedStartTime, 10);
                }

                // Carregar estado do contador regressivo
                if (storedCountdownRemainingTime) {
                    countdownRemainingTime = parseInt(storedCountdownRemainingTime, 10);
                }
                if (storedIsCountdownRunning === 'true') {
                    isCountdownRunning = true;
                } else {
                    isCountdownRunning = false;
                }

                if (isRunning) {
                    startTime = Date.now() - elapsedTime;
                    timerInterval = setInterval(updateTimerDisplay, 1000);
                    startTimerBtn.disabled = true;
                    pauseTimerBtn.disabled = false;
                } else {
                    updateTimerDisplay();
                }

                // Inicializar estado do contador regressivo
                if (isCountdownRunning) {
                    startCountdownBtn.disabled = true;
                    pauseCountdownBtn.disabled = false;
                    resetCountdownBtn.disabled = false;
                    countdownMinutesInput.disabled = true;
                    countdownSecondsInput.disabled = true;
                    // Reiniciar intervalo se estava a correr
                    countdownInterval = setInterval(() => {
                        countdownRemainingTime -= 1000;
                        updateCountdownDisplay();
                    }, 1000);
                } else {
                    // Atualizar display mesmo se não estiver a correr para mostrar o último estado
                    updateCountdownDisplay();
                    // Se o tempo restante for 0, desabilitar botões exceto iniciar
                    if (countdownRemainingTime === 0) {
                        startCountdownBtn.disabled = false;
                        pauseCountdownBtn.disabled = true;
                        resetCountdownBtn.disabled = true;
                    } else {
                        // Se houver tempo restante mas não estiver a correr, permitir iniciar/reiniciar
                        startCountdownBtn.disabled = false;
                        pauseCountdownBtn.disabled = true;
                        resetCountdownBtn.disabled = false;
                    }
                    countdownMinutesInput.disabled = false;
                    countdownSecondsInput.disabled = false;
                }


                userIdDisplay.textContent = 'Dados carregados do navegador'; // Atualizar mensagem
            } catch (e) {
                console.error("Erro ao carregar dados do localStorage:", e);
                showStatusMessage("Erro ao carregar dados localmente. Os dados podem estar corrompidos.", 'error');
                // Limpar dados corrompidos se não for possível analisar
                localStorage.clear();
                allTeams = [];
                // Reverter para modalidades padrão em caso de erro de carregamento
                allModalities = [
                    { id: 'mod_limao', name: 'Corrida do limão', order: 1 },
                    { id: 'mod_laranja', name: 'Dança da laranja', order: 2 },
                    { id: 'mod_pescaria', name: 'Pescaria', order: 3 },
                    { id: 'mod_argola', name: 'Jogo da argola', order: 4 },
                    { id: 'mod_palhaco', name: 'Jogo do palhaço', order: 5 },
                    { id: 'mod_rabo', name: 'Colocar o rabo no burro', order: 6 },
                    { id: 'mod_maca', name: 'Pegar a maça', order: 7 }
                ];
                elapsedTime = 0;
                isRunning = false;
                startTime = 0;
                // Redefinir estado do contador regressivo em caso de erro
                countdownRemainingTime = 0;
                isCountdownRunning = false;
            }
        }


        // --- Funções do Cronômetro de Jogo ---
        function formatTime(ms) {
            const totalSeconds = Math.floor(ms / 1000);
            const hours = Math.floor(totalSeconds / 3600);
            const minutes = Math.floor((totalSeconds % 3600) / 60);
            const seconds = totalSeconds % 60;
            const pad = (num) => num.toString().padStart(2, '0');
            return `${pad(hours)}:${pad(minutes)}:${pad(seconds)}`;
        }

        function updateTimerDisplay() {
            elapsedTime = Date.now() - startTime;
            timerDisplay.textContent = formatTime(elapsedTime);
            saveData();
        }

        function startTimer() {
            if (!isRunning) {
                isRunning = true;
                startTime = Date.now() - elapsedTime; // Ajustar startTime para contabilizar o tempo já decorrido
                timerInterval = setInterval(updateTimerDisplay, 1000);
                startTimerBtn.disabled = true;
                pauseTimerBtn.disabled = false;
                resetTimerBtn.disabled = false;
                saveData();
            }
        }

        function pauseTimer() {
            if (isRunning) {
                clearInterval(timerInterval);
                isRunning = false;
                startTimerBtn.disabled = false;
                pauseTimerBtn.disabled = true;
                saveData();
            }
        }

        function resetTimer() {
            clearInterval(timerInterval);
            elapsedTime = 0;
            isRunning = false;
            timerDisplay.textContent = formatTime(0);
            startTimerBtn.disabled = false;
            pauseTimerBtn.disabled = true;
            resetTimerBtn.disabled = true;
            saveData();
        }

        // --- Funções do Contador Regressivo ---
        function formatCountdownTime(ms) {
            const totalSeconds = Math.max(0, Math.floor(ms / 1000)); // Garantir que não seja negativo
            const minutes = Math.floor(totalSeconds / 60);
            const seconds = totalSeconds % 60;
            const pad = (num) => num.toString().padStart(2, '0');
            return `${pad(minutes)}:${pad(seconds)}`;
        }

        function updateCountdownDisplay() {
            countdownDisplay.textContent = formatCountdownTime(countdownRemainingTime);
            if (countdownRemainingTime <= 0) {
                clearInterval(countdownInterval);
                isCountdownRunning = false;
                startCountdownBtn.disabled = false;
                pauseCountdownBtn.disabled = true;
                resetCountdownBtn.disabled = true; // Desabilitar reset até que um novo tempo seja definido ou iniciado
                countdownMinutesInput.disabled = false; // Habilitar inputs
                countdownSecondsInput.disabled = false;
                showStatusMessage("Contador Regressivo Concluído!", 'status');
                // Opcionalmente, reproduzir um som ou alerta visual aqui
            }
            saveData(); // Salvar estado do contador regressivo
        }

        function startCountdown() {
            let initialMinutes = parseInt(countdownMinutesInput.value) || 0;
            let initialSeconds = parseInt(countdownSecondsInput.value) || 0;

            // Se não estiver a correr E não houver tempo restante, definir o tempo inicial a partir dos inputs
            if (!isCountdownRunning && countdownRemainingTime <= 0) {
                if (initialMinutes === 0 && initialSeconds === 0) {
                    showStatusMessage("Por favor, defina um tempo para o contador regressivo.", 'error');
                    return;
                }
                countdownRemainingTime = (initialMinutes * 60 + initialSeconds) * 1000;
            } else if (!isCountdownRunning && countdownRemainingTime > 0) {
                // Se pausado e houver tempo restante, apenas continuar de onde parou
            }

            if (!isCountdownRunning) {
                isCountdownRunning = true;
                startCountdownBtn.disabled = true;
                pauseCountdownBtn.disabled = false;
                resetCountdownBtn.disabled = false; // Habilitar reset uma vez iniciado
                countdownMinutesInput.disabled = true; // Desabilitar inputs quando a correr
                countdownSecondsInput.disabled = true;

                countdownInterval = setInterval(() => {
                    countdownRemainingTime -= 1000;
                    if (countdownRemainingTime < 0) countdownRemainingTime = 0; // Prevenir tempo negativo
                    updateCountdownDisplay();
                }, 1000);
                saveData();
            }
        }

        function pauseCountdown() {
            if (isCountdownRunning) {
                clearInterval(countdownInterval);
                isCountdownRunning = false;
                startCountdownBtn.disabled = false;
                pauseCountdownBtn.disabled = true;
                saveData();
            }
        }

        function resetCountdown() {
            clearInterval(countdownInterval);
            countdownRemainingTime = 0;
            isCountdownRunning = false;
            countdownDisplay.textContent = formatCountdownTime(0);
            startCountdownBtn.disabled = false; // Habilitar botão iniciar
            pauseCountdownBtn.disabled = true;
            resetCountdownBtn.disabled = true;
            countdownMinutesInput.disabled = false; // Habilitar inputs ao reiniciar
            countdownSecondsInput.value = ''; // Limpar inputs
            countdownMinutesInput.value = ''; // Limpar inputs
            saveData();
        }


        // --- Lógica do Placar ---
        function calculateTotal(teamData) {
            return Object.values(teamData.scores).reduce((sum, score) => sum + score, 0);
        }

        let debounceTimeout = {}; // Para gerenciar o debouncing para mudanças nos inputs

        function renderTeamRow(team) {
            const rowElement = document.createElement('div');
            rowElement.className = 'grid-row';
            const numModalityColumns = allModalities.length;
            rowElement.style.gridTemplateColumns = `1.5fr repeat(${numModalityColumns}, 1fr) 1fr 0.75fr`;

            const teamNameCell = document.createElement('div');
            teamNameCell.className = 'grid-cell';
            const teamNameInput = document.createElement('input');
            teamNameInput.type = 'text';
            teamNameInput.value = team.name;
            teamNameInput.className = 'team-name-input';
            teamNameInput.dataset.teamId = team.id;
            teamNameInput.placeholder = 'Nome da Turma';

            // Debounce para input de nome da equipe
            teamNameInput.addEventListener('input', (e) => {
                const updatedName = e.target.value;
                const teamIndex = allTeams.findIndex(t => t.id === team.id);
                if (teamIndex !== -1) {
                    allTeams[teamIndex].name = updatedName;
                    saveData(); // Salvar imediatamente após a atualização
                }
            });
            teamNameCell.appendChild(teamNameInput);
            rowElement.appendChild(teamNameCell);

            allModalities.forEach(modality => {
                const scoreCell = document.createElement('div');
                scoreCell.className = 'grid-cell';
                const scoreInput = document.createElement('input');
                scoreInput.type = 'number';
                scoreInput.value = team.scores[modality.id] !== undefined ? team.scores[modality.id] : 0;
                scoreInput.className = 'score-input';
                scoreInput.min = "0";
                scoreInput.dataset.teamId = team.id;
                scoreInput.dataset.modalityId = modality.id;

                scoreInput.addEventListener('input', (e) => {
                    clearTimeout(debounceTimeout[team.id]);
                    debounceTimeout[team.id] = setTimeout(() => {
                        const newScore = parseInt(e.target.value) || 0;
                        const currentTeam = allTeams.find(t => t.id === team.id);
                        if (currentTeam) {
                            currentTeam.scores[modality.id] = newScore;
                            currentTeam.total = calculateTotal(currentTeam);
                            saveData();
                            renderScoreboard(); // Re-renderizar para atualizar totais e ordenação
                        }
                    }, 500); // Tempo de debounce em milissegundos
                });
                scoreCell.appendChild(scoreInput);
                rowElement.appendChild(scoreCell);
            });

            const totalScoreCell = document.createElement('div');
            totalScoreCell.className = 'grid-cell total-score';
            totalScoreCell.textContent = team.total;
            rowElement.appendChild(totalScoreCell);

            const actionsCell = document.createElement('div');
            actionsCell.className = 'grid-cell action-buttons';
            const removeBtn = document.createElement('button');
            removeBtn.textContent = 'Remover';
            removeBtn.className = 'remove-team-btn';
            removeBtn.addEventListener('click', () => {
                showConfirmationModal(`Tem certeza que deseja remover a turma "${team.name}"?`, () => {
                    deleteTeam(team.id);
                });
            });
            actionsCell.appendChild(removeBtn);
            rowElement.appendChild(actionsCell);

            scoreboardBody.appendChild(rowElement);
        }

        function addTeam() {
            const newTeam = {
                id: generateUniqueId(),
                name: `Turma ${allTeams.length + 1}`,
                scores: {},
                total: 0
            };
            // Inicializar pontuações para todas as modalidades atuais para 0
            allModalities.forEach(modality => {
                newTeam.scores[modality.id] = 0;
            });
            newTeam.total = calculateTotal(newTeam); // Calcular total inicial
            allTeams.push(newTeam);
            saveData();
            renderScoreboard(); // Re-renderizar para exibir nova equipe e atualizar ordenação
        }

        function deleteTeam(teamId) {
            allTeams = allTeams.filter(team => team.id !== teamId);
            saveData();
            renderScoreboard();
            showStatusMessage("Turma removida com sucesso!", 'status');
        }

        function resetScores() {
            allTeams.forEach(team => {
                for (const modalityId in team.scores) {
                    team.scores[modalityId] = 0;
                }
                team.total = 0;
            });
            saveData();
            renderScoreboard();
            showStatusMessage("Placar reiniciado com sucesso!", 'status');
        }

        // --- Gerenciamento de Modalidades ---
        function addModality() {
            const name = newModalityNameInput.value.trim();
            if (name === "") {
                showStatusMessage("O nome da modalidade não pode ser vazio.", 'error');
                return;
            }
            if (allModalities.some(m => m.name.toLowerCase() === name.toLowerCase())) {
                showStatusMessage("Esta modalidade já existe.", 'error');
                return;
            }

            const newModality = {
                id: generateUniqueId(),
                name: name,
                order: allModalities.length + 1 // Ordenação simples
            };
            allModalities.push(newModality);

            // Adicionar esta nova modalidade com pontuação 0 a todas as equipes existentes
            allTeams.forEach(team => {
                team.scores[newModality.id] = 0;
                team.total = calculateTotal(team); // Recalcular total para equipes existentes
            });

            newModalityNameInput.value = '';
            saveData();
            renderCurrentModalities();
            renderScoreboard(); // Re-renderizar placar para mostrar nova coluna de modalidade
            showStatusMessage("Modalidade adicionada com sucesso!", 'status');
        }

        function updateModality(modalityId, newName) {
            const modality = allModalities.find(m => m.id === modalityId);
            if (modality) {
                const trimmedName = newName.trim();
                if (trimmedName === "") {
                    showStatusMessage("O nome da modalidade não pode ser vazio.", 'error');
                    return false;
                }
                if (allModalities.some(m => m.id !== modalityId && m.name.toLowerCase() === trimmedName.toLowerCase())) {
                    showStatusMessage("Esta modalidade já existe.", 'error');
                    return false;
                }
                modality.name = trimmedName;
                saveData();
                renderScoreboard(); // Re-renderizar placar para cabeçalho atualizado
                showStatusMessage("Modalidade atualizada com sucesso!", 'status');
                return true;
            }
            return false;
        }

        function deleteModality(modalityId, modalityName) {
            showConfirmationModal(`Tem certeza que deseja remover a modalidade "${modalityName}"? Isso removerá todas as pontuações associadas.`, () => {
                allModalities = allModalities.filter(m => m.id !== modalityId);

                // Remover pontuações para esta modalidade de todas as equipes
                allTeams.forEach(team => {
                    delete team.scores[modalityId];
                    team.total = calculateTotal(team); // Recalcular total
                });

                saveData();
                renderCurrentModalities();
                renderScoreboard(); // Re-renderizar placar para remover coluna
                showStatusMessage("Modalidade removida com sucesso!", 'status');
            });
        }

        function renderCurrentModalities() {
            currentModalitiesContainer.innerHTML = ''; // Limpar tags existentes
            allModalities.forEach(modality => {
                const tag = document.createElement('div');
                tag.className = 'modality-tag';

                const nameSpan = document.createElement('span');
                nameSpan.textContent = modality.name;
                nameSpan.style.cursor = 'pointer'; // Indicar que é editável
                nameSpan.title = 'Clique para editar';

                const editInput = document.createElement('input');
                editInput.type = 'text';
                editInput.className = 'edit-modality-input hidden';
                editInput.value = modality.name;

                nameSpan.addEventListener('click', () => {
                    nameSpan.classList.add('hidden');
                    editInput.classList.remove('hidden');
                    editInput.focus();
                });

                const saveEdit = () => {
                    const success = updateModality(modality.id, editInput.value);
                    if (success) {
                        nameSpan.textContent = editInput.value;
                    } else {
                        // Se a atualização falhou (ex: nome vazio/duplicado), reverter input para o original
                        editInput.value = modality.name;
                    }
                    editInput.classList.add('hidden');
                    nameSpan.classList.remove('hidden');
                };

                editInput.addEventListener('blur', saveEdit);
                editInput.addEventListener('keypress', (e) => {
                    if (e.key === 'Enter') {
                        editInput.blur(); // Disparar blur para salvar
                    }
                });

                const deleteBtn = document.createElement('button');
                deleteBtn.textContent = 'x'; // 'x' pequeno para excluir
                deleteBtn.className = 'modality-delete-btn';
                deleteBtn.title = 'Excluir modalidade';
                deleteBtn.addEventListener('click', () => deleteModality(modality.id, modality.name));

                tag.appendChild(nameSpan);
                tag.appendChild(editInput);
                tag.appendChild(deleteBtn);
                currentModalitiesContainer.appendChild(tag);
            });
        }


        // --- Função Principal de Renderização do Placar ---
        function renderScoreboard() {
            loadingIndicator.classList.add('hidden');
            scoreboardBody.innerHTML = ''; // Limpar placar atual
            gridHeader.innerHTML = ''; // Limpar cabeçalho atual também

            // Renderizar cabeçalho dinamicamente
            const numModalityColumns = allModalities.length;
            gridHeader.style.gridTemplateColumns = `1.5fr repeat(${numModalityColumns}, 1fr) 1fr 0.75fr`;
            gridHeader.innerHTML = `
                <div class="grid-cell header-orange-text">EQUIPES</div>
                ${allModalities.map(modality => `<div class="grid-cell header-orange-text">${modality.name}</div>`).join('')}
                <div class="grid-cell header-orange-text">TOTAL</div>
                <div class="grid-cell"></div> `;

            // Ordenar equipes pelo total de pontuação em ordem decrescente
            allTeams.sort((a, b) => b.total - a.total);

            if (allTeams.length === 0) {
                emptyStateMessage.classList.remove('hidden');
            } else {
                emptyStateMessage.classList.add('hidden');
                allTeams.forEach(team => {
                    renderTeamRow(team); // Esta função agora anexa listeners diretamente à nova linha
                });
            }
        }

        // --- Listeners de Eventos ---
        // Botões do Cronômetro de Jogo
        startTimerBtn.addEventListener('click', startTimer);
        pauseTimerBtn.addEventListener('click', pauseTimer);
        resetTimerBtn.addEventListener('click', resetTimer);

        // Botões do Contador Regressivo
        modalConfirmBtn.addEventListener('click', () => {
            if (onConfirmCallback) {
                onConfirmCallback();
            }
            confirmationModal.classList.remove('show');
            onConfirmCallback = null; // Limpar callback
        });

        modalCancelBtn.addEventListener('click', () => {
            confirmationModal.classList.remove('show');
            onConfirmCallback = null; // Limpar callback
        });

        startCountdownBtn.addEventListener('click', startCountdown);
        pauseCountdownBtn.addEventListener('click', pauseCountdown);
        resetCountdownBtn.addEventListener('click', resetCountdown);

        // Listener de mudança de input para habilitar o botão iniciar do contador regressivo
        countdownMinutesInput.addEventListener('input', () => {
            const minutes = parseInt(countdownMinutesInput.value) || 0;
            const seconds = parseInt(countdownSecondsInput.value) || 0;
            if (!isCountdownRunning && (minutes > 0 || seconds > 0)) {
                startCountdownBtn.disabled = false;
            } else if (!isCountdownRunning && minutes === 0 && seconds === 0) {
                 startCountdownBtn.disabled = true;
            }
            saveData(); // Salvar valores de input
        });
        countdownSecondsInput.addEventListener('input', () => {
            const minutes = parseInt(countdownMinutesInput.value) || 0;
            const seconds = parseInt(countdownSecondsInput.value) || 0;
            if (!isCountdownRunning && (minutes > 0 || seconds > 0)) {
                startCountdownBtn.disabled = false;
            } else if (!isCountdownRunning && minutes === 0 && seconds === 0) {
                 startCountdownBtn.disabled = true;
            }
            saveData(); // Salvar valores de input
        });


        // Botões de Ação do Placar
        addTeamBtn.addEventListener('click', addTeam);
        resetScoresBtn.addEventListener('click', () => {
            showConfirmationModal("Tem certeza que deseja reiniciar o placar (zerar todas as pontuações)?", resetScores);
        });

        // Botões do Gerenciador de Modalidades
        addModalityBtn.addEventListener('click', addModality);

        // Carregamento inicial do placar quando o DOM estiver pronto
        document.addEventListener('DOMContentLoaded', () => {
            loadData(); // Carregar dados primeiro, o que também inicializa os estados do cronômetro/contador
            renderScoreboard();
            renderCurrentModalities(); // Também renderizar modalidades

            // Definir o estado inicial dos inputs do contador regressivo a partir dos dados carregados
            if (countdownRemainingTime > 0 && !isCountdownRunning) {
                countdownMinutesInput.value = Math.floor(countdownRemainingTime / 60000);
                countdownSecondsInput.value = Math.floor((countdownRemainingTime % 60000) / 1000);
                startCountdownBtn.disabled = false; // Habilitar iniciar se houver tempo restante
            } else if (countdownRemainingTime === 0 && !isCountdownRunning) {
                startCountdownBtn.disabled = true; // Desabilitar iniciar se nenhum tempo for definido
            }
        });
    </script>
</body>
</html>
