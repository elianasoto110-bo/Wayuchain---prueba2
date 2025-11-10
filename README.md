<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WAYUCHAIN - Energía que Ilumina Confianza</title>
    
    <!-- Carga de Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;800&display=swap" rel="stylesheet">
    
    <style>
        /* Definición de la paleta de colores inspirada en el logo y Wayuu */
        :root {
            /* Colores del Logo: Azul Marino (#113C4E) y Turquesa/Verde Mar (#00A693) */
            --color-wayu-dark: #113C4E; /* Azul Marino (Texto principal, Bordes) */
            --color-wayu-medium: #00A693; /* Turquesa/Verde Mar (Botones, Accents) */
            --color-wayu-light: #F0E7D6; /* Beige/Crema (Fondo de campos de entrada) */
            --color-wayu-accent: #EF4444; /* Rojo Coral/Accent (Para salir/errores) */
            --color-wayu-bg: #EAEFF4; /* Fondo suave Azul-Grisáceo (Fondo general) */
        }
        .bg-wayu-light { background-color: var(--color-wayu-light); }
        .bg-wayu-medium { background-color: var(--color-wayu-medium); }
        .bg-wayu-bg { background-color: var(--color-wayu-bg); }
        .text-wayu-dark { color: var(--color-wayu-dark); }
        .text-wayu-accent { color: var(--color-wayu-accent); }
        .border-wayu-dark { border-color: var(--color-wayu-dark); }
        .hover\:bg-wayu-dark:hover { background-color: var(--color-wayu-dark); }
        .text-shadow { text-shadow: 2px 2px 6px rgba(17, 60, 78, 0.2); }
        body { font-family: 'Inter', sans-serif; }

        /* Estilos específicos para la sección de Trazabilidad (Timeline) */
        .timeline-container {
            position: relative;
            padding-left: 20px;
        }

        .timeline-step {
            /* Línea de tiempo vertical */
            border-left: 3px solid var(--color-wayu-medium); 
            padding: 10px 0 20px 20px;
            position: relative;
        }

        .timeline-step:last-child {
            /* Eliminar la línea en el último elemento */
            border-left: none; 
            padding-bottom: 0;
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
            /* Anillo exterior para resaltar el punto */
            box-shadow: 0 0 0 3px var(--color-wayu-medium); 
        }
    </style>
    
    <!-- Configuración e inicialización de Firebase/Firestore (MANDATORY) -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getAuth, signInAnonymously, signInWithCustomToken, onAuthStateChanged } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
        import { getFirestore, setLogLevel } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

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
<body class="bg-wayu-bg min-h-screen flex flex-col items-center p-4 sm:p-8 relative">

    <!-- Vista de Bienvenida / Login -->
    <div id="login-view" class="w-full max-w-lg mx-auto">
        
        <div class="bg-white p-6 sm:p-10 rounded-xl shadow-2xl border-4 border-wayu-dark text-center">
            
            <!-- Logo de WayuChain (Sustituido por la URL del usuario) -->
            <div class="flex justify-center mb-6">
                <img src='https://i.postimg.cc/0Q4j3gYM/59870.jpg' 
                     alt='Logo de WayuChain' 
                     class="h-24 sm:h-28 object-contain rounded-lg shadow-lg">
            </div>

            <!-- Título Principal -->
            <h1 class="text-6xl sm:text-7xl font-extrabold text-wayu-dark mb-2 text-shadow">
                WAYUCHAIN
            </h1>
            <p id="subtitle" class="text-xl text-gray-600 mb-8 font-semibold">
                Energía que Ilumina Confianza
            </p>

            <!-- 1. Contenedor de Login (Inicial) -->
            <div id="login-container" class="max-w-xs mx-auto mb-10">
                <p class="text-gray-700 leading-relaxed mb-4 font-bold">Inicia Sesión para Personalizar tu Experiencia</p>
                <input type="text" id="username-input" placeholder="Nombre de Usuario (ej: Financiador-1)" 
                        class="w-full bg-wayu-light border-2 border-wayu-dark text-wayu-dark py-3 px-4 rounded-lg leading-tight focus:outline-none focus:bg-white focus:border-wayu-medium mb-4 transition duration-300">
                
                <button id="login-btn"
                    class="w-full bg-wayu-medium hover:bg-wayu-dark text-white font-extrabold py-3 px-12 rounded-full shadow-lg hover:shadow-xl transition duration-300 transform hover:scale-105 disabled:opacity-50 disabled:cursor-not-allowed text-lg uppercase tracking-wider">
                    Login
                </button>
            </div>
            
            <!-- 2. Contenedor de Selección de Rol (Muestra tras el login) -->
            <div id="role-selection-container" class="hidden">
                
                <div class="bg-wayu-light p-4 rounded-lg shadow-inner mb-6 border border-wayu-medium">
                    <p class="text-wayu-dark leading-relaxed max-w-xl mx-auto font-medium">
                        Esta es una **simulación visual de una DApp**, diseñada como experimento para demostrar cómo la tecnología **blockchain** podría usarse para dar **trazabilidad y transparencia** a los fondos que ingresan a la región de La Guajira, asegurando la correcta implementación de **proyectos de energía sostenible**.
                    </p>
                </div>


                <div class="mb-8">
                    <label for="role-selector" class="block text-wayu-dark text-lg font-bold mb-2">Selecciona tu Rol en la Simulación:</label>
                    <div class="relative max-w-xs mx-auto">
                        <select id="role-selector" class="block appearance-none w-full bg-white border-2 border-wayu-dark text-wayu-dark py-3 px-4 pr-8 rounded-lg leading-tight focus:outline-none focus:border-wayu-medium cursor-pointer transition duration-300 shadow-md">
                            <option value="none" disabled selected>Elige un rol...</option>
                            <!-- Nota: Los iconos de los options no se renderizan bien en el select, por lo que he mantenido solo el texto claro -->
                            <option value="financiador">Financiador (Dona los fondos)</option>
                            <option value="ejecutor">Organización Ejecutora</option>
                            <option value="tecnicos">Técnicos</option>
                            <option value="validadores">Validadores</option>
                        </select>
                        <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-wayu-dark">
                            <svg class="fill-current h-4 w-4" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20"><path d="M9.293 12.95l.707.707L15.657 8l-1.414-1.414L10 10.828 5.757 6.586 4.343 8z"/></svg>
                        </div>
                    </div>
                </div>

                <!-- Botón Principal: Entrar a la Simulación -->
                <button id="enter-simulation-btn" disabled
                    class="bg-wayu-medium hover:bg-wayu-dark text-white font-extrabold py-4 px-12 rounded-full shadow-lg hover:shadow-xl transition duration-300 transform hover:scale-105 disabled:opacity-50 disabled:cursor-not-allowed text-xl uppercase tracking-wider">
                    Entrar a la Simulación
                </button>
                <p id="role-message" class="text-sm text-wayu-accent mt-3">Por favor, selecciona un rol antes de entrar.</p>
            </div>
        </div>
    </div>

    <!-- Vista de Dashboard / Simulación de DApp -->
    <div id="dashboard-view" class="w-full max-w-6xl mx-auto hidden mt-6">
        
        <!-- Encabezado del Dashboard -->
        <header class="bg-wayu-dark text-black p-4 sm:p-6 rounded-t-xl flex flex-col sm:flex-row justify-between items-center shadow-xl">
            <h2 class="text-2xl sm:text-3xl font-bold tracking-wider mb-2 sm:mb-0">
                <span id="dashboard-role-title">DASHBOARD</span>
            </h2>
            <div class="flex items-center space-x-4">
                <span id="dashboard-user-id" class="text-sm font-light italic">Usuario: [Nombre]</span>
                <button id="logout-btn" onclick="handleLogout()"
                    class="bg-wayu-accent hover:bg-red-700 text-white font-bold py-2 px-4 rounded-full transition duration-300 transform hover:scale-105 shadow-md">
                    Salir
                </button>
            </div>
        </header>

        <!-- Indicadores Clave -->
        <div class="grid grid-cols-1 md:grid-cols-3 gap-6 p-6 bg-white shadow-inner border-x-4 border-t-0 border-wayu-medium">
            
            <!-- Fondos Totales -->
            <div class="p-4 bg-wayu-light rounded-lg border-2 border-wayu-dark shadow-md">
                <p class="text-sm font-semibold text-gray-600">Fondos Totales (USD)</p>
                <p class="text-3xl font-extrabold text-wayu-dark mt-1">$1,500,000.oo</p>
            </div>
            
            <!-- Gasto Verificado -->
            <div class="p-4 bg-green-100 rounded-lg border-2 border-green-500 shadow-md">
                <p class="text-sm font-semibold text-green-700">Gasto Verificado (Bloques 3, 4)</p>
                <p class="text-3xl font-extrabold text-green-600 mt-1">$68,800.oo</p>
            </div>
            
            <!-- Saldo Disponible -->
            <div class="p-4 bg-yellow-100 rounded-lg border-2 border-yellow-500 shadow-md">
                <p class="text-sm font-semibold text-yellow-700">Saldo Disponible</p>
                <p class="text-3xl font-extrabold text-yellow-600 mt-1">$1,431,200.oo</p>
            </div>
        </div>

        <!-- Sección de Trazabilidad (Timeline) -->
        <div id="trazability-section" class="p-6 sm:p-10 bg-white rounded-b-xl shadow-2xl border-4 border-wayu-medium border-t-0">
            <h2 class="text-3xl font-bold text-wayu-dark mb-8 text-center border-b pb-4">
                Registro Detallado de Transacciones (Blockchain)
            </h2>

            <div class="timeline-container max-w-2xl mx-auto">
                
                <!-- Bloque 1: Recepción de Fondos -->
                <div class="timeline-step">
                    <h3 class="text-xl font-bold text-wayu-dark mb-1">Bloque 1: Recepción de Fondos</h3>
                    <p class="text-sm text-gray-600 mb-2">Wayuchain - Transacción Inicial</p>
                    <div class="bg-wayu-light p-3 rounded-lg shadow-inner border border-wayu-medium">
                        <p class="font-mono text-gray-800 font-bold mb-2">
                            **Origen:** Financiadores Múltiples
                        </p>
                        <ul class="list-disc list-inside text-sm text-gray-800 space-y-1">
                            <li>Fondos Gubernamentales: <span class="text-green-600 font-bold">$1.000.000.oo USD</span></li>
                            <li>Fondos Privados: <span class="text-green-600 font-bold">$500.000.oo USD</span></li>
                            <li class="font-bold border-t mt-2 pt-2 border-gray-400">
                                Total Recibido: <span class="text-green-800 font-extrabold">$1.500.000.oo USD</span>
                            </li>
                        </ul>
                    </div>
                </div>

                <!-- Bloque 2: Asignación de Fondos -->
                <div class="timeline-step">
                    <h3 class="text-xl font-bold text-wayu-dark mb-1">Bloque 2: Asignación de Fondos</h3>
                    <p class="text-sm text-gray-600 mb-2">Se asignan los fondos a la Entidad Ejecutora para dar inicio al proyecto.</p>
                    <div class="bg-wayu-light p-3 rounded-lg shadow-inner border border-wayu-medium">
                        <p class="font-mono text-gray-800 font-bold mb-1">Monto Total Asignado a Entidad Ejecutora</p>
                        <p class="font-mono text-green-700 font-extrabold text-lg">$1.500.000.oo USD</p>
                    </div>
                </div>

                <!-- Bloque 3: Gasto - Ejecutor 1 (MAGNASOLAR) -->
                <div class="timeline-step">
                    <h3 class="text-xl font-bold text-wayu-dark mb-1">Bloque 3: Gasto - Ejecutor 1 (MAGNASOLAR)</h3>
                    <p class="text-sm text-gray-600 mb-2">El proveedor realiza la primera compra de materiales e implemente logistica para el inicio del proyecto.</p>
                    
                    <div class="my-4 p-3 bg-white rounded-lg shadow-md flex justify-center border border-gray-200">
                        <img src="https://www.magnasolar.co/cdn/shop/files/logo_magna_color_2_lineas_70a88a05-5f03-4b21-956d-caf53577e482.png?crop=center&height=120&v=1728418700&width=250" 
                            alt="Logo del Proveedor Magnasolar" 
                            class="h-10 sm:h-12 object-contain"
                            onerror="this.onerror=null; this.src='https://placehold.co/250x50/f3e8d2/6e4a2c?text=MAGNASOLAR+LOGO';">
                    </div>

                    <div class="bg-gray-100 p-3 rounded-lg shadow-inner border border-wayu-medium">
                        <ul class="list-disc list-inside text-sm text-gray-800 space-y-1">
                            <li>Compra 10 páneles solares: <span class="font-bold text-wayu-dark">$37.500.00 USD</span></li>
                            <li>Transporte páneles: <span class="font-bold text-wayu-dark">$3.300.00 USD</span></li>
                            <li>Mano de obra 5 técnicos: <span class="font-bold text-wayu-dark">$4.500.00 USD</span></li>
                            <li class="font-bold border-t mt-2 pt-2 border-gray-300">Total Gastado: <span class="text-wayu-accent font-extrabold">$45.300.00 USD</span></li>
                        </ul>
                    </div>
                </div>

                <!-- Bloque 4: Gasto - Ejecutor 2 (PROELCO) -->
                <div class="timeline-step">
                    <h3 class="text-xl font-bold text-wayu-dark mb-1">Bloque 4: Gasto - Ejecutor 2 (PROELCO)</h3>
                    <p class="text-sm text-gray-600 mb-2">El proveedor realiza la compra de los contadores para ser integrados a la instalación de los paneles solares.</p>
                    
                    <div class="my-4 p-3 bg-white rounded-lg shadow-md flex justify-center border border-gray-200">
                        <img src="https://proelco.com.co/wp-content/uploads/2025/04/LOGO-PROELCO-PRINCIPAL-COLOR.png" 
                            alt="Logo del Proveedor PROELCO" 
                            class="h-10 sm:h-12 object-contain"
                            onerror="this.onerror=null; this.src='https://placehold.co/250x50/f3e8d2/6e4a2c?text=PROELCO+LOGO';">
                    </div>
                    
                    <div class="bg-gray-100 p-3 rounded-lg shadow-inner border border-wayu-medium">
                        <ul class="list-disc list-inside text-sm text-gray-800 space-y-1">
                            <li>Compra 10 contadores para cada panel solar: <span class="font-bold text-wayu-dark">$23.500.00 USD</span></li>
                            <li class="font-bold border-t mt-2 pt-2 border-gray-300">Total Gastado: <span class="text-wayu-accent font-extrabold">$23.500.00 USD</span></li>
                        </ul>
                    </div>
                </div>

                <!-- Bloque 5: Validación y Pago OK -->
                <div class="timeline-step">
                    <h3 class="text-xl font-bold text-green-700 mb-1">Bloque 5: Validación y Pago OK</h3>
                    
                    <div class="my-4 p-3 bg-white rounded-lg shadow-md flex justify-center border border-gray-200 overflow-hidden">
                        <img src="https://www.semana.com/resizer/v2/37PZ66QJFFHY3M65Q4OJ6LTQWA.jpg?auth=0fa994eef6c2419ae8f6628235f1b71cb91c6f33005a6b82636c863b568c04d0&smart=true&quality=75&width=1280&height=720" 
                            alt="Comunidad Wayuu en La Guajira" 
                            class="w-full h-auto object-cover rounded-md"
                            onerror="this.onerror=null; this.src='https://placehold.co/600x150/E0E7FF/6D28D9?text=COMUNIDAD+WAYUU+PROYECTO';">
                    </div>

                    <p class="text-sm text-wayu-dark mb-2 font-semibold">Validadores y Técnicos Confirman Ejecución</p>
                    
                    <div class="bg-green-100 p-4 rounded-lg shadow border-2 border-green-500">
                        <ul class="list-disc list-inside text-green-800 text-sm space-y-1">
                            <li>El equipo validador se desplaza hasta las localidades para realizar la auditoria de la compra de los materiales.</li>
                            <li>Se reciben las facturas originales para su estudio y veracidad.</li>
                            <li>Una vez terminada esta revisión, dan su **OK** en la blockchain para que el hito quede validado.</li>
                        </ul>
                        <p class="text-green-800 text-sm italic mt-3 pt-2 border-t border-green-300 font-bold">
                            ¡Transparencia y ejecución verificada!
                        </p>
                    </div>
                </div>

            </div>

        </div>
    </div>

    <script>
        // Simulación de sesión de usuario y control de vistas
        let currentUserName = null;
        let currentRoleText = null;

        // Función de reemplazo de alert (modal)
        function alert(message) {
            const body = document.body;
            const existingModal = document.getElementById('custom-alert-modal');
            if (existingModal) existingModal.remove();

            const modalHtml = `
                <div id="custom-alert-modal" class="fixed inset-0 bg-gray-600 bg-opacity-75 flex items-center justify-center z-50 transition-opacity duration-300">
                    <div class="bg-white p-6 rounded-xl shadow-xl max-w-sm mx-auto transform transition-all duration-300 scale-100 border-4 border-wayu-dark">
                        <h3 class="text-xl font-bold text-wayu-dark mb-4">Notificación WAYUCHAIN</h3>
                        <p class="text-gray-700 mb-6">${message}</p>
                        <button onclick="document.getElementById('custom-alert-modal').remove()" 
                                class="w-full bg-wayu-medium hover:bg-wayu-dark text-white font-bold py-2 rounded-full transition duration-300 shadow-md">
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
                
                alert(`¡Login exitoso! Bienvenido, ${username}. Ahora selecciona tu rol.`);
            } else {
                alert('Por favor, ingresa un nombre de usuario válido (mínimo 3 caracteres).');
            }
        }

        function handleEnterSimulation() {
            const roleSelector = document.getElementById('role-selector');
            const loginView = document.getElementById('login-view');
            const dashboardView = document.getElementById('dashboard-view');
            
            const selectedRole = roleSelector.value;
            const selectedRoleText = roleSelector.options[roleSelector.selectedIndex].text;
            
            if (selectedRole !== 'none') {
                currentRoleText = selectedRoleText;

                // Ocultar la vista de login y mostrar el Dashboard
                loginView.classList.add('hidden');
                dashboardView.classList.remove('hidden');
                
                // Actualizar los datos del Dashboard
                document.getElementById('dashboard-role-title').textContent = `${currentRoleText.toUpperCase()} DASHBOARD`;
                document.getElementById('dashboard-user-id').textContent = `Usuario: ${currentUserName}`;

                // Scroll to top for a clean view transition
                window.scrollTo({ top: 0, behavior: 'smooth' });

            } else {
                alert('Por favor, selecciona un rol antes de entrar.');
            }
        }
        
        function handleLogout() {
            const loginView = document.getElementById('login-view');
            const dashboardView = document.getElementById('dashboard-view');
            
            // Ocultar el Dashboard y mostrar la vista de Login
            dashboardView.classList.add('hidden');
            loginView.classList.remove('hidden');

            // Resetear la interfaz de login
            document.getElementById('username-input').value = '';
            document.getElementById('login-container').classList.remove('hidden');
            document.getElementById('role-selection-container').classList.add('hidden');
            document.getElementById('role-selector').value = 'none';
            document.getElementById('subtitle').textContent = 'Energía que Ilumina Confianza';
            
            // Asegurar que el botón de entrada se deshabilite al resetear
            document.getElementById('enter-simulation-btn').disabled = true;
            document.getElementById('role-message').classList.remove('hidden');

            alert('Has cerrado sesión correctamente. ¡Gracias por usar la simulación!');
        }
        window.handleLogout = handleLogout; // Exponer al ámbito global para el onclick

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
