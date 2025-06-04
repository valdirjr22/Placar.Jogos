<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Placar dos Jogos</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #F0F4F8; /* Light blue-grey, almost white */
            display: flex;
            justify-content: center;
            align-items: flex-start; /* Align to top for better scrolling on smaller screens */
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
            overflow-x: auto; /* Enable horizontal scrolling for small screens */
        }
        .grid-header, .grid-row {
            display: grid;
            /* grid-template-columns will be set dynamically by JS */
            gap: 8px;
            padding: 12px 0;
            align-items: center;
            text-align: center;
        }
        .grid-header {
            background-color: #2196F3; /* Medium Blue */
            color: white;
            font-weight: 700;
            border-radius: 8px;
            margin-bottom: 12px;
            padding: 12px 8px;
            border: 1px solid #1976D2; /* Darker blue border */
        }
        .grid-header .header-orange-text {
            color: #FF9800; /* Orange text for specific header cells */
        }
        .grid-cell {
            padding: 8px;
            border-radius: 8px;
            background-color: #E3F2FD; /* Light Blue */
            display: flex;
            justify-content: center;
            align-items: center;
            min-width: 80px; /* Minimum width for cells */
        }
        .grid-row:nth-child(even) .grid-cell {
            background-color: #D1E6FA; /* Slightly darker light blue for even rows */
        }
        .grid-row input {
            width: 100%;
            padding: 6px;
            border: 1px solid #90CAF9; /* Lighter blue border */
            border-radius: 6px;
            text-align: center;
            background-color: #ffffff;
            font-weight: 600;
            color: #333;
            transition: border-color 0.2s ease;
        }
        .grid-row input:focus {
            outline: none;
            border-color: #FF9800; /* Orange focus */
            box-shadow: 0 0 0 2px rgba(255, 152, 0, 0.2);
        }
        .grid-row .team-name-display {
            text-align: left;
            padding-left: 12px;
            font-weight: 700;
            min-width: 120px; /* Ensure enough space for team names */
            color: #333333; /* Dark grey text */
            cursor: pointer; /* Indicate it's editable */
        }
        .grid-row .team-name-input {
            text-align: left;
            padding-left: 12px;
            font-weight: 700;
            min-width: 120px;
            color: #333333;
        }
        .grid-row .total-score {
            font-weight: 700;
            color: #333333;
            background-color: #FFECB3; /* Light Orange for total */
            box-shadow: inset 0 0 5px rgba(0,0,0,0.05); /* Subtle inner shadow */
        }
        .add-team-btn, .reset-btn, .edit-team-btn, .remove-team-btn, .modality-btn {
            padding: 10px 16px;
            border-radius: 8px;
            font-weight: 600;
            cursor: pointer;
            transition: background-color 0.3s ease, transform 0.2s ease, box-shadow 0.2s ease;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        }
        .add-team-btn:hover, #addModalityBtn:hover {
            background-color: #F57C00; /* Dark Orange */
            transform: translateY(-2px);
            box-shadow: 0 6px 10px rgba(0, 0, 0, 0.15);
        }
        .reset-btn {
            background-color: #1976D2; /* Dark Blue */
            color: white;
            margin-left: 10px;
        }
        .reset-btn:hover {
            background-color: #1565C0; /* Even darker blue */
            transform: translateY(-2px);
            box-shadow: 0 6px 10px rgba(0, 0, 0, 0.15);
        }
        .edit-team-btn {
            background-color: #2196F3; /* Medium Blue */
            color: white;
            padding: 6px 10px;
            font-size: 0.875rem;
            line-height: 1;
            border-radius: 8px; /* Added border-radius for consistency */
            box-shadow: none;
        }
        .edit-team-btn:hover {
            background-color: #1976D2;
            transform: scale(1.05);
        }
        .remove-team-btn {
            background-color: #1976D2; /* Dark Blue for remove, consistent with reset */
            color: white;
            padding: 6px 10px;
            font-size: 0.875rem;
            line-height: 1;
            border-radius: 8px; /* Added border-radius for consistency */
            box-shadow: none;
        }
        .remove-team-btn:hover {
            background-color: #1565C0;
            transform: scale(1.05);
        }
        .button-group {
            display: flex;
            justify-content: center;
            margin-top: 20px;
            gap: 10px;
        }
        .user-id-display, .status-message {
            background-color: #E3F2FD; /* Light Blue */
            padding: 8px 12px;
            border-radius: 8px;
            margin-bottom: 16px;
            font-size: 0.9rem;
            color: #333333;
            text-align: center;
            word-break: break-all; /* Ensures long IDs wrap */
        }
        .status-message {
            background-color: #FFF3E0; /* Light orange for status */
            color: #E65100; /* Dark orange text */
            border: 1px solid #FFB74D; /* Medium orange border */
        }
        .error-message {
            background-color: #FFEBE9; /* Very light red for errors */
            color: #C62828; /* Dark red text */
            border: 1px solid #EF9A9A; /* Medium red border */
        }

        /* Modal styles */
        .modal {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background-color: rgba(0, 0, 0, 0.6); /* Darker overlay */
            display: flex;
            justify-content: center;
            align-items: center;
            z-index: 1000;
            opacity: 0;
            visibility: hidden;
            transition: opacity 0.3s ease, visibility 0.3s ease;
        }
        .modal.visible {
            opacity: 1;
            visibility: visible;
        }
        .modal-content {
            background-color: white;
            padding: 30px;
            border-radius: 12px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3); /* Stronger shadow */
            text-align: center;
            max-width: 400px;
            width: 90%;
            transform: translateY(-20px);
            transition: transform 0.3s ease;
        }
        .modal.visible .modal-content {
            transform: translateY(0);
        }
        .modal-buttons {
            margin-top: 20px;
            display: flex;
            justify-content: center;
            gap: 15px;
        }
        .modal-buttons button {
            padding: 10px 20px;
            border-radius: 8px;
            font-weight: 600;
            cursor: pointer;
            transition: background-color 0.3s ease, transform 0.2s ease;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
        }
        .modal-buttons .confirm-btn {
            background-color: #FF9800; /* Orange confirm */
            color: white;
        }
        .modal-buttons .confirm-btn:hover {
            background-color: #F57C00;
            transform: translateY(-1px);
        }
        .modal-buttons .cancel-btn {
            background-color: #E0E0E0; /* Light grey cancel */
            color: #333;
        }
        .modal-buttons .cancel-btn:hover {
            background-color: #BDBDBD;
            transform: translateY(-1px);
        }
        .empty-state-message {
            text-align: center;
            padding: 40px;
            color: #6b7280;
            font-size: 1.1rem;
            background-color: #f9fafb;
            border-radius: 8px;
            margin-top: 20px;
            border: 1px dashed #d1d5db;
        }
        .save-feedback {
            font-size: 0.75rem;
            color: #2e7d32;
            margin-left: 8px;
            opacity: 0;
            transition: opacity 0.3s ease;
        }
        .save-feedback.visible {
            opacity: 1;
        }
        /* Timer styles */
        .timer-container {
            background-color: #E3F2FD; /* Light Blue */
            border-radius: 12px;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.08);
            padding: 20px;
            margin-bottom: 24px;
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 15px;
        }
        #timerDisplay {
            font-size: 3.5rem; /* Larger font for time */
            font-weight: 700;
            color: #1976D2; /* Dark Blue */
            letter-spacing: 2px;
            text-shadow: 1px 1px 2px rgba(0,0,0,0.1);
        }
        .timer-buttons button {
            padding: 12px 24px;
            border-radius: 10px;
            font-weight: 700;
            font-size: 1.1rem;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.15);
            transition: all 0.3s ease;
        }
        .timer-buttons button:hover:not(:disabled) {
            transform: translateY(-3px);
            box-shadow: 0 6px 12px rgba(0, 0, 0, 0.2);
        }
        .timer-buttons button:disabled {
            opacity: 0.6;
            cursor: not-allowed;
            box-shadow: none;
        }
        .timer-buttons .bg-green-600 { background-color: #FF9800; } /* Orange */
        .timer-buttons .bg-green-600:hover:not(:disabled) { background-color: #F57C00; }
        .timer-buttons .bg-yellow-600 { background-color: #2196F3; color: white; } /* Blue */
        .timer-buttons .bg-yellow-600:hover:not(:disabled) { background-color: #1976D2; }
        .timer-buttons .bg-red-600 { background-color: #1976D2; } /* Dark Blue */
        .timer-buttons .bg-red-600:hover:not(:disabled) { background-color: #1565C0; }

        /* Modality Manager Styles */
        .modality-manager-container {
            background-color: #F0F4F8; /* Same as body background */
            border-radius: 12px;
            padding: 20px;
            margin-bottom: 24px;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.05);
        }
        .modality-tag {
            background-color: #FF9800; /* Orange */
            color: white;
            padding: 6px 12px;
            border-radius: 20px;
            display: flex;
            align-items: center;
            gap: 8px;
            font-size: 0.9rem;
            transition: background-color 0.2s ease;
        }
        .modality-tag:hover:not(.editing) {
            background-color: #F57C00; /* Darker Orange on hover */
        }
        .modality-tag .modality-name-display {
            flex-grow: 1;
            cursor: pointer; /* Indicate it's editable */
        }
        .modality-tag .modality-name-input {
            background-color: white;
            border: 1px solid #90CAF9; /* Lighter blue border */
            border-radius: 4px;
            padding: 2px 6px;
            color: #333;
            font-weight: 600;
            width: auto; /* Adjust width dynamically */
            min-width: 50px;
            max-width: 150px; /* Limit max width */
            box-shadow: inset 0 1px 3px rgba(0,0,0,0.1);
        }
        .modality-tag .modality-action-btn {
            background-color: #2196F3; /* Blue for modality actions */
            color: white;
            padding: 4px 8px;
            border-radius: 6px;
            font-size: 0.75rem;
            cursor: pointer;
            transition: background-color 0.2s ease, transform 0.1s ease;
            box-shadow: none;
            margin-left: 4px;
        }
        .modality-tag .modality-action-btn:hover {
            background-color: #1976D2;
            transform: translateY(-1px);
        }
    </style>
</head>
<body>
    <div class="scoreboard-container">
        <h1 class="text-4xl font-extrabold text-center text-blue-800 mb-6">SENAC PAULISTA</h1>
        <h1 class="text-3xl font-bold text-center text-gray-800 mb-6">Placar dos Jogos</h1>
        <div class="user-id-display" id="userIdDisplay">Dados salvos localmente no navegador</div>
        <div class="status-message hidden" id="statusMessage"></div>

        <div class="timer-container">
            <div id="timerDisplay">00:00:00</div>
            <div class="timer-buttons">
                <button id="startTimerBtn" class="bg-green-600 text-white">Iniciar</button>
                <button id="pauseTimerBtn" class="bg-yellow-600 text-white">Pausar</button>
                <button id="resetTimerBtn" class="bg-red-600 text-white">Reiniciar</button>
            </div>
        </div>
        <div class="modality-manager-container">
            <h2 class="text-xl font-semibold text-gray-700 mb-4">Gerenciar Modalidades</h2>
            <div class="flex flex-wrap items-center gap-4 mb-4">
                <input type="text" id="newModalityName" placeholder="Nome da Nova Modalidade" class="flex-grow p-2 border rounded-md" maxlength="20">
                <button id="addModalityBtn" class="add-team-btn modality-btn">Adicionar Modalidade</button>
            </div>
            <div id="currentModalities" class="flex flex-wrap gap-2">
                </div>
        </div>
        <div class="grid-header" id="gridHeader">
            </div>

        <div id="scoreboard-body">
            <div id="loadingIndicator" class="empty-state-message hidden">Carregando placar...</div>
            <div id="emptyState" class="empty-state-message hidden">
                Nenhuma turma adicionada ainda. Clique em "Adicionar Turma" para começar!
            </div>
            </div>

        <div class="button-group">
            <button id="addTeamBtn" class="add-team-btn">Adicionar Turma</button>
            <button id="resetScoresBtn" class="reset-btn">Reiniciar Placar</button>
        </div>
    </div>

    <div id="confirmationModal" class="modal hidden">
        <div class="modal-content">
            <p id="modalMessage" class="text-lg font-semibold text-gray-700 mb-4"></p>
            <div class="modal-buttons">
                <button id="modalConfirmBtn" class="confirm-btn">Confirmar</button>
                <button id="modalCancelBtn" class="cancel-btn">Cancelar</button>
            </div>
        </div>
    </div>

    <script type="module">
        // Variáveis para armazenar dados localmente
        let allTeams = [];
        let allModalities = [];

        const statusMessageElement = document.getElementById('statusMessage');
        const loadingIndicator = document.getElementById('loadingIndicator');
        const emptyStateMessage = document.getElementById('emptyState');
        const scoreboardBody = document.getElementById('scoreboard-body');
        const gridHeader = document.getElementById('gridHeader');
        const userIdDisplay = document.getElementById('userIdDisplay'); // Get the user ID display element

        // Timer variables
        let timerInterval = null;
        let startTime = 0;
        let elapsedTime = 0;
        let isRunning = false;

        const timerDisplay = document.getElementById('timerDisplay');
        const startTimerBtn = document.getElementById('startTimerBtn');
        const pauseTimerBtn = document.getElementById('pauseTimerBtn');
        const resetTimerBtn = document.getElementById('resetTimerBtn');

        // Modality variables
        const newModalityNameInput = document.getElementById('newModalityName');
        const addModalityBtn = document.getElementById('addModalityBtn');
        const currentModalitiesContainer = document.getElementById('currentModalities');

        // Function to generate a simple unique ID (for local use)
        function generateUniqueId() {
            return '_' + Math.random().toString(36).substr(2, 9);
        }

        // --- Persistence Functions (localStorage) ---
        function saveData() {
            try {
                localStorage.setItem('allTeams', JSON.stringify(allTeams));
                localStorage.setItem('allModalities', JSON.stringify(allModalities));
                localStorage.setItem('timerElapsedTime', elapsedTime.toString());
                localStorage.setItem('timerIsRunning', isRunning.toString());
                localStorage.setItem('timerStartTime', startTime.toString());
                userIdDisplay.textContent = 'Dados salvos localmente no navegador'; // Update message
            } catch (e) {
                console.error("Error saving data to localStorage:", e);
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

                if (storedTeams) {
                    allTeams = JSON.parse(storedTeams);
                } else {
                    allTeams = [];
                }

                if (storedModalities) {
                    allModalities = JSON.parse(storedModalities);
                    // Ensure modalities have an 'order' property for sorting, or add it if missing
                    allModalities.forEach((m, index) => {
                        if (m.order === undefined) {
                            m.order = index + 1;
                        }
                    });
                    allModalities.sort((a, b) => a.order - b.order);
                } else {
                    allModalities = [];
                }

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

                if (isRunning) {
                    // If timer was running, restart it to continue from where it left off
                    startTime = Date.now() - elapsedTime; // Recalculate startTime
                    timerInterval = setInterval(updateTimerDisplay, 1000);
                    startTimerBtn.disabled = true;
                    pauseTimerBtn.disabled = false;
                } else {
                    // If not running, just update the display
                    updateTimerDisplay();
                }

                userIdDisplay.textContent = 'Dados carregados do navegador'; // Update message
            } catch (e) {
                console.error("Error loading data from localStorage:", e);
                showStatusMessage("Erro ao carregar dados localmente. Os dados podem estar corrompidos.", 'error');
                // Clear corrupted data if unable to parse
                localStorage.clear();
                allTeams = [];
                allModalities = [];
                elapsedTime = 0;
                isRunning = false;
                startTime = 0;
            }
        }
        // --- End Persistence Functions ---


        // Function to display status messages
        function showStatusMessage(message, type = 'status') {
            statusMessageElement.textContent = message;
            statusMessageElement.classList.remove('hidden', 'status-message', 'error-message');
            statusMessageElement.classList.add(type === 'error' ? 'error-message' : 'status-message');
            setTimeout(() => {
                statusMessageElement.classList.add('hidden');
            }, 5000); // Hide after 5 seconds
        }

        // Function to show custom confirmation modal
        function showConfirmationModal(message, onConfirm) {
            const modal = document.getElementById('confirmationModal');
            const modalMessage = document.getElementById('modalMessage');
            const modalConfirmBtn = document.getElementById('modalConfirmBtn');
            const modalCancelBtn = document.getElementById('modalCancelBtn');

            modalMessage.textContent = message;
            modal.classList.add('visible'); // Use 'visible' class for transition

            return new Promise((resolve) => {
                const handleConfirm = () => {
                    modal.classList.remove('visible');
                    modalConfirmBtn.removeEventListener('click', handleConfirm);
                    modalCancelBtn.removeEventListener('click', handleCancel);
                    onConfirm(); // Execute the action if confirmed
                    resolve(true);
                };

                const handleCancel = () => {
                    modal.classList.remove('visible');
                    modalConfirmBtn.removeEventListener('click', handleConfirm);
                    modalCancelBtn.removeEventListener('click', handleCancel);
                    resolve(false);
                };

                modalConfirmBtn.addEventListener('click', handleConfirm);
                modalCancelBtn.addEventListener('click', handleCancel);
            });
        }

        // Timer functions
        function formatTime(ms) {
            const totalSeconds = Math.floor(ms / 1000);
            const hours = Math.floor((totalSeconds % 86400) / 3600); // Max 24 hours
            const minutes = Math.floor((totalSeconds % 3600) / 60);
            const seconds = totalSeconds % 60;

            const pad = (num) => num.toString().padStart(2, '0');
            return `${pad(hours)}:${pad(minutes)}:${pad(seconds)}`;
        }

        function updateTimerDisplay() {
            const currentTime = Date.now();
            const currentElapsedTime = elapsedTime + (isRunning ? (currentTime - startTime) : 0);
            timerDisplay.textContent = formatTime(currentElapsedTime);
            saveData(); // Save timer state on update
        }

        function startTimer() {
            if (!isRunning) {
                startTime = Date.now() - elapsedTime;
                timerInterval = setInterval(updateTimerDisplay, 1000); // Update every second
                isRunning = true;
                startTimerBtn.disabled = true;
                pauseTimerBtn.disabled = false;
                saveData(); // Save timer state
            }
        }

        function pauseTimer() {
            if (isRunning) {
                clearInterval(timerInterval);
                elapsedTime = Date.now() - startTime;
                isRunning = false;
                startTimerBtn.disabled = false;
                pauseTimerBtn.disabled = true;
                saveData(); // Save timer state
            }
        }

        function resetTimer() {
            clearInterval(timerInterval);
            elapsedTime = 0;
            isRunning = false;
            timerDisplay.textContent = formatTime(0);
            startTimerBtn.disabled = false;
            pauseTimerBtn.disabled = true;
            saveData(); // Save timer state
        }

        // Add event listeners for timer buttons
        startTimerBtn.addEventListener('click', startTimer);
        pauseTimerBtn.addEventListener('click', pauseTimer);
        resetTimerBtn.addEventListener('click', resetTimer);

        // Function to calculate total score for a team
        function calculateTotal(teamData) {
            let total = 0;
            if (teamData.scores) {
                for (const modalityId in teamData.scores) {
                    total += parseInt(teamData.scores[modalityId] || 0);
                }
            }
            return total;
        }

        // Function to render a single team row
        function renderTeamRow(team) {
            const row = document.createElement('div');
            row.className = 'grid-row';
            row.dataset.id = team.id; // Store unique ID

            // Set grid-template-columns dynamically based on modalities
            const numModalityColumns = allModalities.length;
            // Adjusted grid-template-columns to accommodate both Edit and Remove buttons
            row.style.gridTemplateColumns = `1.5fr repeat(${numModalityColumns}, 1fr) 1fr 0.75fr`; // Team Name, Modalities, Total, Buttons

            let modalityInputsHtml = '';
            allModalities.forEach(modality => {
                const score = team.scores ? (team.scores[modality.id] || 0) : 0;
                modalityInputsHtml += `
                    <div class="grid-cell">
                        <input type="number" class="score-input" data-modality-id="${modality.id}" value="${score}" title="Pontuação para ${modality.name}">
                    </div>
                `;
            });

            row.innerHTML = `
                <div class="grid-cell team-name-cell">
                    <span class="team-name-display">${team.name}</span>
                    <input type="text" class="team-name-input w-full hidden" value="${team.name}" placeholder="Nome da Equipe" maxlength="30" title="Nome da Equipe">
                </div>
                ${modalityInputsHtml}
                <div class="grid-cell total-score">${team.total || 0}</div>
                <div class="grid-cell relative flex flex-col items-center justify-center gap-1">
                    <button class="edit-team-btn w-full" title="Editar Turma">Editar</button>
                    <button class="remove-team-btn w-full" title="Remover Turma">Remover</button>
                    <span class="save-feedback absolute right-0 top-1/2 -translate-y-1/2">✔ Salvo!</span>
                </div>
            `;
            scoreboardBody.appendChild(row);

            // Get elements for editing
            const teamNameDisplay = row.querySelector('.team-name-display');
            const teamNameInput = row.querySelector('.team-name-input');
            const editButton = row.querySelector('.edit-team-btn');
            const removeButton = row.querySelector('.remove-team-btn'); // Get the new remove button

            // Add event listeners for score inputs
            const scoreInputs = row.querySelectorAll('.score-input');
            scoreInputs.forEach(input => {
                let timeoutId;
                input.addEventListener('input', () => {
                    clearTimeout(timeoutId);
                    timeoutId = setTimeout(() => {
                        updateTeamScore(team.id, row);
                    }, 500); // Debounce input to avoid too many updates
                });
            });

            // Event listener for the Edit button
            if (editButton) {
                editButton.addEventListener('click', () => {
                    // Toggle visibility of display span and input field
                    teamNameDisplay.classList.add('hidden');
                    teamNameInput.classList.remove('hidden');
                    teamNameInput.focus();
                });
            } else {
                console.error("[renderTeamRow] Edit button not found for team:", team.id);
            }

            // Event listener for the Remove button
            if (removeButton) {
                removeButton.addEventListener('click', () => {
                    console.log(`[renderTeamRow] Remove button clicked for team ID: ${team.id}`);
                    showConfirmationModal('Tem certeza que deseja remover esta turma? Esta ação é irreversível.', () => {
                        console.log(`[deleteTeam] Confirmação recebida para remover turma com ID: ${team.id}`);
                        deleteTeam(team.id);
                    });
                });
            } else {
                console.error("[renderTeamRow] Remove button not found for team:", team.id);
            }

            // Event listener for saving team name on blur or Enter
            const saveTeamName = () => {
                const newName = teamNameInput.value.trim();
                if (newName && newName !== teamNameDisplay.textContent) {
                    updateTeamScore(team.id, row); // This will update the name
                }
                // Revert to display mode
                teamNameDisplay.textContent = teamNameInput.value;
                teamNameDisplay.classList.remove('hidden');
                teamNameInput.classList.add('hidden');
            };

            teamNameInput.addEventListener('blur', saveTeamName);
            teamNameInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter') {
                    saveTeamName();
                }
            });
        }

        // Function to update team score in local array
        function updateTeamScore(teamId, rowElement) {
            const teamIndex = allTeams.findIndex(t => t.id === teamId);
            if (teamIndex === -1) {
                showStatusMessage("Erro: Turma não encontrada.", 'error');
                return;
            }

            const teamName = rowElement.querySelector('.team-name-input').value;
            const updatedScores = {};
            rowElement.querySelectorAll('.score-input').forEach(input => {
                const modalityId = input.dataset.modalityId;
                updatedScores[modalityId] = parseInt(input.value) || 0;
            });

            const newTeamData = {
                id: teamId,
                name: teamName,
                scores: updatedScores,
            };
            newTeamData.total = calculateTotal(newTeamData); // Recalculate total

            allTeams[teamIndex] = newTeamData; // Update the local array
            saveData(); // Save data after every update
            renderScoreboard(); // Re-render the entire scoreboard to update totals and order

            // Show save feedback - this part might be tricky with full re-renders
            // For now, let's keep it simple, as the whole row is recreated.
            // A more advanced solution would be to update the specific cell without re-rendering the whole row.
            // If you want feedback, you might need to find the new row after re-render based on team.id
            const updatedRowElement = scoreboardBody.querySelector(`[data-id="${teamId}"]`);
            if (updatedRowElement) {
                const saveFeedback = updatedRowElement.querySelector('.save-feedback');
                if (saveFeedback) {
                     saveFeedback.classList.add('visible');
                     setTimeout(() => {
                         saveFeedback.classList.remove('visible');
                     }, 1500);
                }
            }
        }

        // Function to add a new team
        document.getElementById('addTeamBtn').addEventListener('click', () => {
            const newTeamId = generateUniqueId();
            const initialScores = {};
            allModalities.forEach(modality => {
                initialScores[modality.id] = 0;
            });

            const newTeam = {
                id: newTeamId,
                name: `Nova Equipe ${allTeams.length + 1}`,
                scores: initialScores,
                total: 0,
            };
            allTeams.push(newTeam);
            saveData(); // Save data after adding team
            renderScoreboard(); // Re-render to show the new team
            showStatusMessage("Nova turma adicionada com sucesso!");
        });

        // Function to delete a team
        function deleteTeam(teamId) {
            allTeams = allTeams.filter(team => team.id !== teamId);
            saveData(); // Save data after deletion
            renderScoreboard(); // Re-render after deletion
            showStatusMessage("Turma eliminada com sucesso!");
        }

        // Function to reset all scores
        document.getElementById('resetScoresBtn').addEventListener('click', () => {
            showConfirmationModal('Tem certeza que deseja reiniciar todas as pontuações para zero? Esta ação é irreversível.', () => {
                allTeams.forEach(team => {
                    for (const modalityId in team.scores) {
                        team.scores[modalityId] = 0;
                    }
                    team.total = calculateTotal(team); // Recalculate total
                });
                saveData(); // Save data after reset
                renderScoreboard(); // Re-render with reset scores
                showStatusMessage("Todas as pontuações foram reiniciadas com sucesso!");
            });
        });

        // Modality Management Functions
        function addModality() {
            const modalityName = newModalityNameInput.value.trim();
            if (!modalityName) {
                showStatusMessage("O nome da modalidade não pode estar vazio.", 'error');
                return;
            }
            if (allModalities.some(m => m.name.toLowerCase() === modalityName.toLowerCase())) {
                showStatusMessage("Uma modalidade com este nome já existe.", 'error');
                return;
            }

            const newModalityId = generateUniqueId();
            const newModality = {
                id: newModalityId,
                name: modalityName,
                order: allModalities.length + 1,
            };
            allModalities.push(newModality);
            allModalities.sort((a,b) => a.order - b.order); // Ensure order is maintained
            saveData(); // Save data after adding modality
            renderCurrentModalities(); // Re-render modality tags

            // Update existing teams with the new modality score initialized to 0
            allTeams.forEach(team => {
                team.scores[newModalityId] = 0;
                team.total = calculateTotal(team); // Recalculate total
            });
            renderScoreboard(); // Re-render teams with new column
            showStatusMessage(`Modalidade "${modalityName}" adicionada com sucesso!`);
            newModalityNameInput.value = ''; // Clear input
        }

        function updateModality(modalityId, newName) {
            const trimmedName = newName.trim();
            if (!trimmedName) {
                showStatusMessage("O nome da modalidade não pode estar vazio.", 'error');
                return;
            }
            if (allModalities.some(m => m.id !== modalityId && m.name.toLowerCase() === trimmedName.toLowerCase())) {
                showStatusMessage("Uma modalidade com este nome já existe.", 'error');
                return;
            }

            const modalityIndex = allModalities.findIndex(m => m.id === modalityId);
            if (modalityIndex !== -1) {
                allModalities[modalityIndex].name = trimmedName;
                saveData(); // Save data after updating modality
                renderCurrentModalities(); // Re-render modality tags
                renderScoreboard(); // Re-render scoreboard header and team rows with updated modality name
                showStatusMessage(`Modalidade atualizada para "${trimmedName}" com sucesso!`);
            }
        }

        function deleteModality(modalityId, modalityName) {
            showConfirmationModal(`Tem certeza que deseja excluir a modalidade "${modalityName}"? Isso removerá todas as pontuações associadas a ela para todas as turmas.`, () => {
                allModalities = allModalities.filter(modality => modality.id !== modalityId);
                saveData(); // Save data after deleting modality
                renderCurrentModalities(); // Re-render modality tags

                // Remove modality score from all teams
                allTeams.forEach(team => {
                    if (team.scores.hasOwnProperty(modalityId)) {
                        delete team.scores[modalityId]; // Remove the score for this modality
                    }
                    team.total = calculateTotal(team); // Recalculate total
                });
                renderScoreboard(); // Re-render teams with removed column
                showStatusMessage(`Modalidade "${modalityName}" eliminada com sucesso!`);
            });
        }

        addModalityBtn.addEventListener('click', addModality);

        // Function to render current modalities as tags
        function renderCurrentModalities() {
            currentModalitiesContainer.innerHTML = '';
            allModalities.forEach(modality => {
                const tag = document.createElement('span');
                tag.className = 'modality-tag';
                tag.dataset.modalityId = modality.id; // Store ID for editing/deleting

                const nameDisplay = document.createElement('span');
                nameDisplay.className = 'modality-name-display';
                nameDisplay.textContent = modality.name;
                tag.appendChild(nameDisplay);

                // Edit button for modality
                const editBtn = document.createElement('button');
                editBtn.className = 'modality-action-btn';
                editBtn.title = 'Editar Modalidade';
                editBtn.textContent = 'Editar';
                editBtn.dataset.modalityId = modality.id;
                tag.appendChild(editBtn);

                // Delete button for modality
                const deleteBtn = document.createElement('button');
                deleteBtn.className = 'modality-action-btn';
                deleteBtn.title = 'Excluir Modalidade';
                deleteBtn.textContent = 'Excluir';
                deleteBtn.dataset.modalityId = modality.id;
                deleteBtn.dataset.modalityName = modality.name;
                tag.appendChild(deleteBtn);

                currentModalitiesContainer.appendChild(tag);

                // Event listener for the Edit button
                editBtn.addEventListener('click', () => {
                    // Prevent editing if already editing another tag
                    if (document.querySelector('.modality-tag.editing')) {
                        showStatusMessage("Já está a editar outra modalidade. Salve ou cancele primeiro.", "status");
                        return;
                    }

                    tag.classList.add('editing'); // Add class to indicate editing state

                    const input = document.createElement('input');
                    input.type = 'text';
                    input.className = 'modality-name-input';
                    input.value = nameDisplay.textContent;
                    input.maxLength = 20; // Limit input length
                    input.title = 'Editar Nome da Modalidade';

                    // Replace text span with input
                    tag.replaceChild(input, nameDisplay);
                    input.focus();

                    // Hide buttons during editing
                    editBtn.classList.add('hidden');
                    deleteBtn.classList.add('hidden');

                    const saveChanges = () => {
                        const newName = input.value.trim();
                        if (newName && newName !== modality.name) { // Only update if name has changed
                            updateModality(modality.id, newName);
                        } else {
                            // If name didn't change or was empty, just revert
                            tag.classList.remove('editing');
                            tag.replaceChild(nameDisplay, input); // Replace input with text span
                            nameDisplay.textContent = modality.name; // Revert to original name
                            editBtn.classList.remove('hidden');
                            deleteBtn.classList.remove('hidden');
                        }
                    };

                    input.addEventListener('blur', saveChanges); // Save on blur
                    input.addEventListener('keypress', (e) => {
                        if (e.key === 'Enter') {
                            input.blur(); // Trigger blur to save changes
                        }
                    });
                });

                // Add event listener to delete modality button
                deleteBtn.addEventListener('click', (event) => {
                    const modalityId = event.target.dataset.modalityId;
                    const modalityName = event.target.dataset.modalityName;
                    deleteModality(modalityId, modalityName);
                });
            });
        }

        // Function to render the entire scoreboard from local data
        function renderScoreboard() {
            loadingIndicator.classList.add('hidden'); // Hide loading indicator once data is ready

            scoreboardBody.innerHTML = ''; // Clear current display
            gridHeader.innerHTML = ''; // Clear header as well

            // Render header dynamically
            const numModalityColumns = allModalities.length;
            gridHeader.style.gridTemplateColumns = `1.5fr repeat(${numModalityColumns}, 1fr) 1fr 0.75fr`;
            gridHeader.innerHTML = `
                <div class="grid-cell header-orange-text">EQUIPES</div>
                ${allModalities.map(modality => `<div class="grid-cell header-orange-text">${modality.name}</div>`).join('')}
                <div class="grid-cell header-orange-text">TOTAL</div>
                <div class="grid-cell"></div> `;

            // Sort teams by total score in descending order
            allTeams.sort((a, b) => b.total - a.total);

            if (allTeams.length === 0) {
                emptyStateMessage.classList.remove('hidden');
            } else {
                emptyStateMessage.classList.add('hidden');
                allTeams.forEach(team => {
                    renderTeamRow(team); // This function now attaches listeners directly to the new row
                });
            }
        }

        // Initial load of the scoreboard when the DOM is ready
        document.addEventListener('DOMContentLoaded', () => {
            loadData(); // Load data first
            renderScoreboard();
            renderCurrentModalities(); // Also render modalities
            // Timer button state will be handled by loadData based on stored isRunning
        });
    </script>
</body>
</html>
