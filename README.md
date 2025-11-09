<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WAYUCHAIN - Simulación DApp</title>
    <!-- Carga de Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;800&display=swap" rel="stylesheet">
    <style>
        /* Definición de la paleta de colores tierra (Tailwind Custom Colors) */
        :root {
            --color-wayu-light: #f3e8d2; /* Beige claro */
            --color-wayu-medium: #c6a05d; /* Dorado tierra */
            --color-wayu-dark: #6e4a2c; /* Café oscuro */
            --color-wayu-accent: #38a169; /* Verde de acento */
        }
        .bg-wayu-light { background-color: var(--color-wayu-light); }
        .bg-wayu-medium { background-color: var(--color-wayu-medium); }
        .text-wayu-dark { color: var(--color-wayu-dark); }
        .border-wayu-dark { border-color: var(--color-wayu-dark); }
        .hover\:bg-wayu-dark:hover { background-color: var(--color-wayu-dark); }
        .text-shadow { text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.1); }
        body { font-family: 'Inter', sans-serif; }

        /* Estilo para el logo de la bombilla con mapa de Colombia */
        .logo-bulb {
            width: 120px;
            height: 120px;
            position: relative;
            margin: 0 auto 1.5rem;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        .logo-bulb-filament {
            position: absolute;
            z-index: 10;
            width: 100%;
            height: 100%;
            display: flex;
            align-items: center;
            justify-content: center;
        }
        /* Estilo para el mapa de Colombia con colores tierra */
        .logo-bulb-map {
            position: absolute;
            z-index: 5;
            width: 80px;
            height: 80px;
            border-radius: 50%;
            background-image: linear-gradient(to right bottom, #c6a05d, #a88849, #8d6d39);
            box-shadow: inset 0 0 10px rgba(0, 0, 0, 0.2);
            opacity: 0.8;
            /* SVG simulado de mapa de Colombia (simplified) */
            mask-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 512 512'%3E%3Cpath fill='%23000' d='M256 0c141.4 0 256 114.6 256 256S397.4 512 256 512 0 397.4 0 256 114.6 0 256 0zM256 48a208 208 0 1 0 0 416 208 208 0 1 0 0-416zm0 48a160 160 0 1 0 0 320 160 160 0 1 0 0-320zm0 48a112 112 0 1 0 0 224 112 112 0 1 0 0-224zM256 192a64 64 0 1 0 0 128 64 64 0 1 0 0-128z'/%3E%3C/svg%3E");
            -webkit-mask-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 512 512'%3E%3Cpath fill='%23000' d='M256 0c141.4 0 256 114.6 256 256S397.4 512 256 512 0 397.4 0 256 114.6 0 256 0zM256 48a208 208 0 1 0 0 416 208 208 0 1 0 0-416zm0 48a160 160 0 1 0 0 320 160 160 0 1 0 0-320zm0 48a112 112 0 1 0 0 224 112 112 0 1 0 0-224zM256 192a64 64 0 1 0 0 128 64 64 0 1 0 0-128z'/%3E%3C/svg%3E");
            mask-repeat: no-repeat;
            mask-position: center;
        }

        /* Estilos específicos para la sección de Trazabilidad */
        .timeline-container {
            position: relative;
            padding-left: 20px;
        }

        .timeline-step {
            border-left: 3px solid var(--color-wayu-medium);
            padding: 10px 0 20px 20px;
            position: relative;
        }

        .timeline-step:last-child {
            border-left: none; /* Eliminar la línea en el último elemento */
        }

        .timeline-step::before {
            content: ' ';
            position: absolute;
            left: -8px;
            top: 15px;
            width: 15px;
            height: 15px;
            border-radius: 50%;
            background-color: var(--color-wayu-dark);
            border: 3px solid var(--color-wayu-light);
            box-shadow: 0 0 0 3px var(--color-wayu-medium);
        }
    </style>
    <!-- Firebase SDKs -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, doc, getDoc, onSnapshot, collection, query, where, addDoc, getDocs, setDoc } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";
        import { setLogLevel } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

        // Global variables provided by the Canvas environment
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        const firebaseConfig = JSON.parse(typeof __firebase_config !== 'undefined' ? __firebase_config : '{}');
        const initialAuthToken = typeof __initial_auth_token !== 'undefined' ? __initial_auth_token : null;

        if (Object.keys(firebaseConfig).length > 0) {
            window.app = initializeApp(firebaseConfig);
            window.db = getFirestore(window.app);
            window.auth = getAuth(window.app);
            setLogLevel('Debug');

            // Authentication logic
            onAuthStateChanged(window.auth, async (user) => {
                if (user) {
                    window.userId = user.uid;
                    console.log("User authenticated:", window.userId);
                    // Check if the user is a new user and store their data (optional in this simulation)
                } else {
                    // Sign in using custom token or anonymously
                    try {
                        if (initialAuthToken) {
                            await signInWithCustomToken(window.auth, initialAuthToken);
                        } else {
                            await signInAnonymously(window.auth);
                        }
                    } catch (error) {
                        console.error("Firebase Auth Error:", error);
                    }
                }
            });
        } else {
            console.warn("Firebase configuration not found. Running in simulation mode without persistent data.");
        }
    </script>
</head>
<body class="bg-wayu-light min-h-screen flex flex-col items-center p-4 sm:p-8">

    <!-- Contenedor Principal Centrado -->
    <div class="w-full max-w-4xl mx-auto">
        
        <!-- Pantalla de Bienvenida/Login (Card Principal) -->
        <div id="app-card" class="bg-white p-6 sm:p-10 rounded-xl shadow-2xl border-4 border-wayu-dark text-center">
            
            <!-- Logo -->
            <div class="logo-bulb">
                <div class="logo-bulb-map"></div>
                <div class="logo-bulb-filament">
                    <!-- Icono de bombilla (usando SVG de Lucide) -->
                    <svg class="w-20 h-20 text-yellow-500 transform -rotate-12" xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"><path d="M15 14c.2-1 .9-1.8 1.8-2.3a4 4 0 0 0-.5-6c-.4-.5-1-.9-1.5-1.4.1-.1.1-.2.2-.3 1.2-1.2 2.7-2 4-2.7.2-.1.4-.2.5-.3.4-.2.8-.4 1.2-.6.7-.4 1.4-.7 2-1-.3 0-.6 0-.9-.1C18.6 1.4 17 1 15.5 1 12.8 1 10 2.2 8.4 4.3 8 4.7 7.7 5 7.4 5.3 7 5.7 6.6 6 6.3 6.3 6 6.6 5.6 7 5.3 7.3c-.5.4-.9.9-1.3 1.4-.1.1-.2.2-.3.3-1.4 1.6-2.2 3.5-2.2 5.5 0 2 .8 3.9 2.2 5.5 1.6 1.4 3.5 2.2 5.5 2.2 2 0 3.9-.8 5.5-2.2.4-.4.8-.8 1.2-1.2-.1-.1-.1-.2-.2-.3-.5-.4-1.2-.8-1.9-1.2-1.5-.8-3.3-1.2-5.2-1.2z" stroke="#6e4a2c" fill="#facc15"/><path d="M12 21a2 2 0 0 1-2-2v-3h4v3a2 2 0 0 1-2 2z" fill="#6e4a2c" stroke="#6e4a2c"/></svg>
                </div>
            </div>

            <!-- Título -->
            <h1 class="text-6xl sm:text-7xl font-extrabold text-wayu-dark mb-4 text-shadow">
                WAYUCHAIN
            </h1>
            <p id="subtitle" class="text-xl text-gray-600 mb-8 font-semibold">
                Energía que Ilumina Confianza
            </p>

            <!-- Contenido de Login -->
            <div id="login-container" class="max-w-xs mx-auto mb-10">
                <p class="text-gray-700 leading-relaxed mb-4 font-bold">Inicia Sesión para Acceder a la Simulación</p>
                <input type="text" id="username-input" placeholder="Nombre de Usuario (ej: Financiador-1)" 
                       class="w-full bg-wayu-light border-2 border-wayu-dark text-wayu-dark py-3 px-4 rounded-lg leading-tight focus:outline-none focus:bg-white focus:border-wayu-medium mb-4 transition duration-300">
                
                <button id="login-btn"
                    class="w-full bg-wayu-medium hover:bg-wayu-dark text-white font-extrabold py-3 px-12 rounded-full shadow-lg hover:shadow-xl transition duration-300 transform hover:scale-105 disabled:opacity-50 disabled:cursor-not-allowed text-lg uppercase tracking-wider">
                    Login
                </button>
            </div>
            
            <!-- Contenido de Selección de Rol (Inicialmente oculto) -->
            <div id="role-selection-container" class="hidden">
                <p id="welcome-message" class="text-gray-700 leading-relaxed max-w-xl mx-auto mb-6">
                    Esta es una **simulación visual y sencilla de una DApp (Aplicación Descentralizada)**, diseñada como experimento para demostrar cómo la tecnología blockchain podría usarse para garantizar la trazabilidad de los fondos y la ejecución de proyectos en comunidades, enfocándose en la transparencia de cada transacción.
                </p>

                <div class="mb-8">
                    <label for="role-selector" class="block text-wayu-dark text-lg font-bold mb-2">Selecciona tu Rol en la Simulación:</label>
                    <div class="relative max-w-xs mx-auto">
                        <select id="role-selector" class="block appearance-none w-full bg-wayu-light border-2 border-wayu-dark text-wayu-dark py-3 px-4 pr-8 rounded-lg leading-tight focus:outline-none focus:bg-white focus:border-wayu-medium cursor-pointer transition duration-300">
                            <option value="none" disabled selected>Elige un rol...</option>
                            <!-- ROLES ACTUALIZADOS según la solicitud anterior -->
                            <option value="financiador">Financiador (Dona los fondos)</option>
                            <option value="ejecutor">Organización Ejecutora</option>
                            <option value="tecnicos">Técnicos</option>
                            <option value="validadores">Validadores</option>
                        </select>
                        <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-wayu-dark">
                            <!-- Icono de flecha hacia abajo -->
                            <svg class="fill-current h-4 w-4" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20"><path d="M9.293 12.95l.707.707L15.657 8l-1.414-1.414L10 10.828 5.757 6.586 4.343 8z"/></svg>
                        </div>
                    </div>
                </div>

                <!-- Botón de Entrada (Línea 195) -->
                <button id="enter-simulation-btn"
                    class="bg-wayu-medium hover:bg-wayu-dark text-white font-extrabold py-4 px-12 rounded-full shadow-lg hover:shadow-xl transition duration-300 transform hover:scale-105 disabled:opacity-50 disabled:cursor-not-allowed text-xl uppercase tracking-wider">
                    Entrar a la Simulación
                </button>
                <p id="role-message" class="text-sm text-red-500 mt-3 hidden">Por favor, selecciona un rol antes de entrar.</p>
            </div>
        
            <!-- Sección de Trazabilidad (incluida dentro del app-card) -->
            <div id="trazability-section" class="mt-12 p-6 sm:p-10 bg-white rounded-xl shadow-2xl border-4 border-wayu-medium max-w-4xl mx-auto">
                <h2 class="text-3xl font-bold text-wayu-dark mb-6 text-center">
                    Registro de Trazabilidad en Blockchain
                </h2>

                <div class="timeline-container max-w-2xl mx-auto">
                    
                    <!-- Paso 1: Fondos Recibidos (Bloque Inicial) -->
                    <div class="timeline-step">
                        <h3 class="text-xl font-bold text-wayu-dark mb-1">Bloque 1: Recepción de Fondos</h3>
                        <p class="text-sm text-gray-600 mb-2">Wayuchain - Transacción Inicial</p>
                        <div class="bg-wayu-light p-3 rounded-lg shadow-inner border border-wayu-medium">
                            <p class="font-mono text-gray-800">
                                **Origen:** Gobierno / Fondos Privados
                            </p>
                            <p class="font-mono text-gray-800">
                                **Monto Total Recibido:** <span class="text-green-600 font-bold">$1.000.000.oo USD</span>
                            </p>
                        </div>
                    </div>

                    <!-- Paso 2: Asignación a Ejecutores -->
                    <div class="timeline-step">
                        <h3 class="text-xl font-bold text-wayu-dark mb-1">Bloque 2: Asignación a Ejecutores</h3>
                        <p class="text-sm text-gray-600 mb-2">Ejecutores reciben presupuesto inicial</p>
                        <div class="bg-wayu-light p-3 rounded-lg shadow-inner border border-wayu-medium grid grid-cols-1 sm:grid-cols-2 gap-4">
                            <div>
                                <p class="font-mono text-gray-800 font-bold">Asignación Wayuchain</p>
                                <p class="font-mono text-gray-800">$500.000.oo USD</p>
                            </div>
                            <div>
                                <p class="font-mono text-gray-800 font-bold">Asignación Entidad Ejecutora</p>
                                <p class="font-mono text-gray-800">$500.000.oo USD</p>
                            </div>
                        </div>
                    </div>

                    <!-- Paso 3: Gasto del Ejecutor 1 -->
                    <div class="timeline-step">
                        <h3 class="text-xl font-bold text-wayu-dark mb-1">Bloque 3: Gasto - Ejecutor 1</h3>
                        <p class="text-sm text-gray-600 mb-2">Compra de Paneles Solares e Instalación</p>
                        <div class="bg-gray-100 p-3 rounded-lg shadow-inner border border-wayu-medium">
                            <ul class="list-disc list-inside text-sm text-gray-800 space-y-1">
                                <li>Compra 10 páneles solares: $37.500.00 USD</li>
                                <li>Transporte páneles: $3.300.00 USD</li>
                                <li>Mano de obra 5 técnicos: $4.500.00 USD</li>
                                <li class="font-bold">Total Gastado: $45.300.00 USD</li>
                            </ul>
                        </div>
                    </div>
                    
                    <!-- Paso 4: Gasto del Ejecutor 2 -->
                    <div class="timeline-step">
                        <h3 class="text-xl font-bold text-wayu-dark mb-1">Bloque 4: Gasto - Ejecutor 2</h3>
                        <p class="text-sm text-gray-600 mb-2">Compra de Contadores</p>
                        <div class="bg-gray-100 p-3 rounded-lg shadow-inner border border-wayu-medium">
                            <ul class="list-disc list-inside text-sm text-gray-800 space-y-1">
                                <li>Compra 10 contadores para cada panel solar: $23.500.00 USD</li>
                                <li class="font-bold">Total Gastado: $23.500.00 USD</li>
                            </ul>
                        </div>
                    </div>

                    <!-- Paso 5: Validación y Aprobación (Bloque de Confirmación) -->
                    <div class="timeline-step">
                        <h3 class="text-xl font-bold text-wayu-accent mb-1">Bloque 5: Validación y Pago OK</h3>
                        <p class="text-sm text-wayu-dark mb-2 font-semibold">Validadores y Técnicos Confirman Ejecución</p>
                        <div class="bg-green-100 p-4 rounded-lg shadow border-2 border-wayu-accent">
                            <p class="text-green-800 text-sm italic">
                                Validadores (Comunidad Wayu) + Técnicos han realizado la auditoría de estos procesos y han dado su **OK** para realizar los pagos directamente a los proveedores. <span class="font-bold">Transparencia y ejecución verificada.</span>
                            </p>
                        </div>
                    </div>

                </div>

            </div>
        </div>
    </div>

    <script>
        // Simulación de sesión de usuario
        let currentUserName = null;

        // Función de reemplazo de alert (modal)
        function alert(message) {
            const body = document.body;
            const existingModal = document.getElementById('custom-alert-modal');
            if (existingModal) existingModal.remove();

            const modalHtml = `
                <div id="custom-alert-modal" class="fixed inset-0 bg-gray-600 bg-opacity-75 flex items-center justify-center z-50 transition-opacity duration-300">
                    <div class="bg-white p-6 rounded-lg shadow-xl max-w-sm mx-auto transform transition-all duration-300 scale-100 border-4 border-wayu-dark">
                        <h3 class="text-xl font-bold text-wayu-dark mb-4">Notificación WAYUCHAIN</h3>
                        <p class="text-gray-700 mb-6">${message}</p>
                        <button onclick="document.getElementById('custom-alert-modal').remove()" 
                                class="w-full bg-wayu-medium hover:bg-wayu-dark text-white font-bold py-2 rounded-lg transition duration-300">
                            Aceptar
                        </button>
                    </div>
                </div>
            `;
            body.insertAdjacentHTML('beforeend', modalHtml);
        }

        function handleLogin() {
            const usernameInput = document.getElementById('username-input');
            const loginContainer = document.getElementById('login-container');
            const roleSelectionContainer = document.getElementById('role-selection-container');
            const subtitle = document.getElementById('subtitle');
            
            const username = usernameInput.value.trim();

            if (username.length > 2) {
                currentUserName = username;
                
                // Ocultar login y mostrar selección de rol
                loginContainer.classList.add('hidden');
                roleSelectionContainer.classList.remove('hidden');

                // Personalizar el mensaje de bienvenida y subtítulo
                subtitle.innerHTML = `Bienvenido, <span class="text-wayu-dark">${username}</span>. Elige tu rol.`;
                
                alert(`¡Login exitoso! Bienvenido, ${username}.`);
            } else {
                alert('Por favor, ingresa un nombre de usuario válido (mínimo 3 caracteres).');
            }
        }

        function handleEnterSimulation() {
            const roleSelector = document.getElementById('role-selector');
            const selectedRole = roleSelector.value;
            const selectedRoleText = roleSelector.options[roleSelector.selectedIndex].text;
            
            if (selectedRole !== 'none') {
                console.log(`Entrando a la simulación con el usuario: ${currentUserName} y el rol: ${selectedRole}`);
                // [RESTAURADO] Volver a mostrar una alerta en lugar de cambiar de pantalla
                alert(`¡Simulación iniciada! Usuario: ${currentUserName}. Rol: ${selectedRoleText}.`);
            } else {
                alert('Por favor, selecciona un rol antes de entrar.');
            }
        }

        document.addEventListener('DOMContentLoaded', () => {
            const usernameInput = document.getElementById('username-input');
            const loginBtn = document.getElementById('login-btn');
            const roleSelector = document.getElementById('role-selector');
            const enterBtn = document.getElementById('enter-simulation-btn');
            
            // Lógica inicial del botón Enter
            const checkRoleSelection = () => {
                const selectedRole = roleSelector.value;
                const message = document.getElementById('role-message');
                if (selectedRole === 'none') {
                    enterBtn.disabled = true;
                    message.classList.remove('hidden');
                } else {
                    enterBtn.disabled = false;
                    message.classList.add('hidden');
                }
            };

            // Event Listeners
            loginBtn.addEventListener('click', handleLogin);
            usernameInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter') {
                    handleLogin();
                }
            });

            roleSelector.addEventListener('change', checkRoleSelection);
            enterBtn.addEventListener('click', handleEnterSimulation);

            // Ejecutar chequeo inicial
            checkRoleSelection();
        });
    </script>
</body>
</html>
