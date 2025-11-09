<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>WAYUCHAIN - Simulación DApp</title>
    

<script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;600;800&display=swap" rel="stylesheet">
    <style>
        /* Definición de la paleta de colores inspirada en el logo y Wayuu */
        :root {
            /* Colores del Logo: Azul Marino (#113C4E) y Beige/Crema (#F0E7D6) */
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
        .text-shadow { text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.1); }
        body { font-family: 'Inter', sans-serif; }

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
        
        /* Estilo para el botón de Logout */
        #logout-btn {
            position: absolute;
            top: 20px;
            right: 20px;
        }

        /* Estilo para las imágenes dentro del select */
        .role-option-image {
            width: 20px;
            height: 20px;
            margin-right: 8px;
            vertical-align: middle;
            display: inline-block; /* Asegura que la imagen y el texto estén en línea */
        }
        /* Para que el texto no se vea cortado si la imagen es un poco grande */
        .role-option-text {
            display: inline-block;
            vertical-align: middle;
        }
    </style>
    

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
<body class="bg-wayu-bg min-h-screen flex flex-col items-center p-4 sm:p-8 relative">

    

<div id="login-view" class="w-full max-w-md mx-auto">
        
        

<div class="bg-white p-6 sm:p-10 rounded-xl shadow-2xl border-4 border-wayu-dark text-center">
            
            

<div class="flex justify-center mb-6">
                <!-- Nuevo Logo de WayuChain -->
                <img src="uploaded:image_eb592d.jpg-5bbaae5c-15e9-4708-a84a-3ac352bb8796" 
                     alt="Logo de WayuChain - Energía que Ilumina Confianza" 
                     class="h-24 sm:h-28 object-contain">
            </div>

            

<h1 class="text-6xl sm:text-7xl font-extrabold text-wayu-dark mb-4 text-shadow">
                WAYUCHAIN
            </h1>
            <p id="subtitle" class="text-xl text-gray-600 mb-8 font-semibold">
                Energía que Ilumina Confianza
            </p>

            

<div id="login-container" class="max-w-xs mx-auto mb-10">
                <p class="text-gray-700 leading-relaxed mb-4 font-bold">Inicia Sesión para Acceder a la Simulación</p>
                <input type="text" id="username-input" placeholder="Nombre de Usuario (ej: Financiador-1)" 
                       class="w-full bg-wayu-light border-2 border-wayu-dark text-wayu-dark py-3 px-4 rounded-lg leading-tight focus:outline-none focus:bg-white focus:border-wayu-medium mb-4 transition duration-300">
                
                <button id="login-btn"
                    class="w-full bg-wayu-medium hover:bg-wayu-dark text-white font-extrabold py-3 px-12 rounded-full shadow-lg hover:shadow-xl transition duration-300 transform hover:scale-105 disabled:opacity-50 disabled:cursor-not-allowed text-lg uppercase tracking-wider">
                    Login
                </button>
            </div>
            
            

<div id="role-selection-container" class="hidden">
                <p id="welcome-message" class="text-gray-700 leading-relaxed max-w-xl mx-auto mb-6">
                    Esta es una **simulación visual y sencilla de una DApp (Aplicación Descentralizada)**, diseñada como experimento para demostrar cómo la tecnología blockchain podría usarse para garantizar la trazabilidad de los fondos y la ejecución de proyectos en comunidades, enfocándose en la transparencia de cada transacción.
                </p>

                <div class="mb-8">
                    <label for="role-selector" class="block text-wayu-dark text-lg font-bold mb-2">Selecciona tu Rol en la Simulación:</label>
                    <div class="relative max-w-xs mx-auto">
                        <select id="role-selector" class="block appearance-none w-full bg-wayu-light border-2 border-wayu-dark text-wayu-dark py-3 px-4 pr-8 rounded-lg leading-tight focus:outline-none focus:bg-white focus:border-wayu-medium cursor-pointer transition duration-300">
                            <option value="none" disabled selected>Elige un rol...</option>
                            <option value="financiador"><img src="https://img.icons8.com/ios-filled/50/113C4E/money-bag.png" class="role-option-image" alt="Financiador"><span class="role-option-text">Financiador (Dona los fondos)</span></option>
                            <option value="ejecutor"><img src="https://img.icons8.com/ios-filled/50/113C4E/organization.png" class="role-option-image" alt="Organización Ejecutora"><span class="role-option-text">Organización Ejecutora</span></option>
                            <option value="tecnicos"><img src="https://img.icons8.com/ios-filled/50/113C4E/wrench.png" class="role-option-image" alt="Técnicos"><span class="role-option-text">Técnicos</span></option>
                            <option value="validadores"><img src="https://img.icons8.com/ios-filled/50/113C4E/checked--v1.png" class="role-option-image" alt="Validadores"><span class="role-option-text">Validadores</span></option>
                        </select>
                        <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-wayu-dark">
                            

<svg class="fill-current h-4 w-4" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20"><path d="M9.293 12.95l.707.707L15.657 8l-1.414-1.414L10 10.828 5.757 6.586 4.343 8z"/></svg>
                        </div>
                    </div>
                </div>

                

<button id="enter-simulation-btn"
                    class="bg-wayu-medium hover:bg-wayu-dark text-white font-extrabold py-4 px-12 rounded-full shadow-lg hover:shadow-xl transition duration-300 transform hover:scale-105 disabled:opacity-50 disabled:cursor-not-allowed text-xl uppercase tracking-wider">
                    Entrar a la Simulación
                </button>
                <p id="role-message" class="text-sm text-wayu-accent mt-3 hidden">Por favor, selecciona un rol antes de entrar.</p>
            </div>
        </div>
    </div>

    

<div id="dashboard-view" class="w-full max-w-6xl mx-auto hidden">
        
        

<header class="bg-wayu-dark text-white p-4 sm:p-6 rounded-t-xl flex justify-between items-center shadow-xl">
            <h2 class="text-2xl sm:text-3xl font-bold tracking-wider">
                <span id="dashboard-role-title">DASHBOARD</span>
            </h2>
            <div class="flex items-center space-x-4">
                <span id="dashboard-user-id" class="text-sm font-light italic">Usuario: [Nombre]</span>
                <button id="logout-btn" onclick="handleLogout()"
                    class="bg-wayu-accent hover:bg-red-700 text-white font-bold py-2 px-4 rounded-full transition duration-300 transform hover:scale-105">
                    Salir
                </button>
            </div>
        </header>

        

<div class="grid grid-cols-1 md:grid-cols-3 gap-6 p-6 bg-white shadow-inner">
            

<div class="p-4 bg-wayu-light rounded-lg border-2 border-wayu-medium shadow-md">
                <p class="text-sm font-semibold text-gray-600">Fondos Totales (USD)</p>
                

<p class="text-3xl font-extrabold text-wayu-dark mt-1">$1,500,000.oo</p>
            </div>
            

<div class="p-4 bg-green-100 rounded-lg border-2 border-green-400 shadow-md">
                <p class="text-sm font-semibold text-green-700">Gasto Verificado (Bloques 3, 4)</p>
                <p class="text-3xl font-extrabold text-green-600 mt-1">$68,800.oo</p>
            </div>
            

<div class="p-4 bg-yellow-100 rounded-lg border-2 border-yellow-400 shadow-md">
                <p class="text-sm font-semibold text-yellow-700">Saldo Disponible</p>
                

<p class="text-3xl font-extrabold text-yellow-600 mt-1">$1,431,200.oo</p>
            </div>
        </div>

        

<div id="trazability-section" class="p-6 sm:p-10 bg-white rounded-b-xl shadow-2xl border-4 border-wayu-medium border-t-0">
            <h2 class="text-3xl font-bold text-wayu-dark mb-6 text-center border-b pb-4">
                Registro Detallado de Transacciones (Blockchain)
            </h2>

            <div class="timeline-container max-w-2xl mx-auto">
                
                

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

                

<div class="timeline-step">
                    <h3 class="text-xl font-bold text-wayu-dark mb-1">Bloque 2: Asignación de Fondos</h3>
                    

<p class="text-sm text-gray-600 mb-2">Se asignan los fondos a la Entidad Ejecutora para dar inicio al proyecto.</p>
                    <div class="bg-wayu-light p-3 rounded-lg shadow-inner border border-wayu-medium">
                        <p class="font-mono text-gray-800 font-bold mb-1">Monto Total Asignado a Entidad Ejecutora</p>
                        

<p class="font-mono text-green-700 font-extrabold text-lg">$1.500.000.oo USD</p>
                    </div>
                </div>

                

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
                            <li>Compra 10 páneles solares: $37.500.00 USD</li>
                            <li>Transporte páneles: $3.300.00 USD</li>
                            <li>Mano de obra 5 técnicos: $4.500.00 USD</li>
                            <li class="font-bold">Total Gastado: $45.300.00 USD</li>
                        </ul>
                    </div>
                </div>

                

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
                            <li>Compra 10 contadores para cada panel solar: $23.500.00 USD</li>
                            <li class="font-bold">Total Gastado: $23.500.00 USD</li>
                        </ul>
                    </div>
                </div>

                

<div class="timeline-step">
                    <h3 class="text-xl font-bold text-wayu-accent mb-1">Bloque 5: Validación y Pago OK</h3>
                    
                    

<div class="my-4 p-3 bg-white rounded-lg shadow-md flex justify-center border border-gray-200 overflow-hidden">
                        <img src="https://www.semana.com/resizer/v2/37PZ66QJFFHY3M65Q4OJ6LTQWA.jpg?auth=0fa994eef6c2419ae8f6628235f1b71cb91c6f33005a6b82636c863b568c04d0&smart=true&quality=75&width=1280&height=720" 
                             alt="Comunidad Wayuu en La Guajira" 
                             class="w-full h-auto object-cover rounded-md"
                             onerror="this.onerror=null; this.src='https://placehold.co/600x150/E0E7FF/6D28D9?text=COMUNIDAD+WAYUU+PROYECTO';">
                    </div>

                    <p class="text-sm text-wayu-dark mb-2 font-semibold">Validadores y Técnicos Confirman Ejecución</p>
                    
                    <div class="bg-green-100 p-4 rounded-lg shadow border-2 border-wayu-medium">
                        

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
        // Simulación de sesión de usuario
        let currentUserName = null;
        let currentRoleText = null;

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
            const loginView = document.getElementById('login-view');
            const dashboardView = document.getElementById('dashboard-view');
            
            const selectedRole = roleSelector.value;
            const selectedRoleText = roleSelector.options[roleSelector.selectedIndex].text;
            
            if (selectedRole !== 'none') {
                currentRoleText = selectedRoleText;

                // [MODIFICACIÓN] Ocultar la vista de login y mostrar el Dashboard
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
            
            alert('Has cerrado sesión correctamente.');
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
