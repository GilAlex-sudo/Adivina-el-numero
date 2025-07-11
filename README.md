# Adivina-el-numero

<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Adivina el Número</title>
    <!-- Enlace a Tailwind CSS CDN para utilidades básicas, aunque se usará CSS personalizado para el juego -->
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;700&display=swap" rel="stylesheet">
    <style>
        /* Estilos personalizados para el juego */
        body {
            font-family: 'Inter', sans-serif;
            background-color: #2d3748; /* Color de fondo oscuro */
            color: #e2e8f0; /* Color de texto claro */
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            padding: 1rem;
            box-sizing: border-box;
        }

        .game-container {
            background-color: #1a202c; /* Fondo del contenedor del juego */
            border-radius: 1.5rem; /* Bordes más redondeados */
            padding: 2.5rem;
            box-shadow: 0 10px 20px rgba(0, 0, 0, 0.4); /* Sombra más pronunciada */
            text-align: center;
            max-width: 500px;
            width: 100%;
            border: 2px solid #4a5568; /* Borde sutil */
        }

        h1 {
            font-size: 2.5rem;
            font-weight: bold;
            color: #a78bfa; /* Morado vibrante para el título */
            margin-bottom: 1.5rem;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5); /* Sombra de texto */
        }

        p {
            font-size: 1.125rem;
            margin-bottom: 1.5rem;
        }

        input[type="number"] {
            background-color: #4a5568; /* Fondo del input */
            border: 2px solid #6b7280; /* Borde del input */
            border-radius: 0.75rem; /* Bordes redondeados */
            padding: 0.75rem 1rem;
            font-size: 1.25rem;
            color: #e2e8f0;
            width: calc(100% - 2rem); /* Ancho completo con padding */
            max-width: 200px;
            text-align: center;
            margin-bottom: 1rem;
            outline: none; /* Eliminar el contorno al enfocar */
            transition: border-color 0.3s ease;
        }

        input[type="number"]:focus {
            border-color: #a78bfa; /* Resaltar al enfocar */
        }

        button {
            background: linear-gradient(145deg, #8b5cf6, #a78bfa); /* Degradado para el botón */
            color: white;
            padding: 0.75rem 2rem;
            border-radius: 0.75rem;
            font-size: 1.25rem;
            font-weight: bold;
            cursor: pointer;
            border: none;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.3); /* Sombra del botón */
            transition: transform 0.2s ease, box-shadow 0.2s ease;
            margin: 0.5rem;
            outline: none;
        }

        button:hover {
            transform: translateY(-2px); /* Efecto de elevación al pasar el ratón */
            box-shadow: 0 6px 10px rgba(0, 0, 0, 0.4);
        }

        button:active {
            transform: translateY(0); /* Efecto de presión al hacer clic */
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.3);
        }

        #message {
            margin-top: 1.5rem;
            font-size: 1.25rem;
            font-weight: bold;
            min-height: 2rem; /* Para evitar saltos de diseño */
        }

        #attempts {
            font-size: 1rem;
            color: #cbd5e0;
            margin-top: 0.5rem;
        }

        /* Estilos para el botón de reinicio */
        #resetButton {
            background: linear-gradient(145deg, #ef4444, #dc2626); /* Degradado rojo */
        }
        #resetButton:hover {
            background: linear-gradient(145deg, #dc2626, #b91c1c);
        }

        /* Responsive adjustments */
        @media (max-width: 600px) {
            .game-container {
                padding: 1.5rem;
            }
            h1 {
                font-size: 2rem;
            }
            input[type="number"], button {
                font-size: 1rem;
                padding: 0.6rem 1.5rem;
            }
            p, #message {
                font-size: 1rem;
            }
        }
    </style>
</head>
<body>
    <div class="game-container">
        <h1>Adivina el Número</h1>
        <p>Estoy pensando en un número entre 1 y 100.</p>
        <p>¿Puedes adivinarlo?</p>

        <input type="number" id="guessInput" min="1" max="100" placeholder="Introduce tu número">
        <br>
        <button id="checkButton">Comprobar</button>
        <button id="resetButton" class="hidden">Reiniciar Juego</button>

        <p id="message"></p>
        <p id="attempts">Intentos: 0</p>
    </div>

    <script>
        // Declaración de variables globales para el juego
        let secretNumber; // El número que el usuario debe adivinar
        let attempts;     // Contador de intentos
        let gameOver;     // Bandera para saber si el juego ha terminado

        // Referencias a elementos del DOM
        const guessInput = document.getElementById('guessInput');
        const checkButton = document.getElementById('checkButton');
        const resetButton = document.getElementById('resetButton');
        const messageDisplay = document.getElementById('message');
        const attemptsDisplay = document.getElementById('attempts');

        /**
         * Inicializa o reinicia el estado del juego.
         * Genera un nuevo número secreto, reinicia el contador de intentos
         * y actualiza la interfaz de usuario.
         */
        function initializeGame() {
            secretNumber = Math.floor(Math.random() * 100) + 1; // Número aleatorio entre 1 y 100
            attempts = 0;
            gameOver = false;

            // Restablecer la interfaz de usuario
            messageDisplay.textContent = ''; // Limpiar el mensaje
            attemptsDisplay.textContent = 'Intentos: 0'; // Reiniciar el contador de intentos
            guessInput.value = ''; // Limpiar el campo de entrada
            guessInput.disabled = false; // Habilitar el campo de entrada
            checkButton.disabled = false; // Habilitar el botón de comprobar
            resetButton.classList.add('hidden'); // Ocultar el botón de reinicio
            guessInput.focus(); // Poner el foco en el campo de entrada
        }

        /**
         * Maneja la lógica cuando el usuario hace un intento de adivinar el número.
         */
        function checkGuess() {
            if (gameOver) return; // No hacer nada si el juego ha terminado

            const userGuess = parseInt(guessInput.value); // Obtener el número del usuario y convertirlo a entero

            // Validar la entrada del usuario
            if (isNaN(userGuess) || userGuess < 1 || userGuess > 100) {
                messageDisplay.textContent = 'Por favor, introduce un número válido entre 1 y 100.';
                messageDisplay.style.color = '#f87171'; // Rojo para errores
                return; // Salir de la función si la entrada es inválida
            }

            attempts++; // Incrementar el contador de intentos
            attemptsDisplay.textContent = `Intentos: ${attempts}`; // Actualizar el display de intentos

            // Lógica principal del juego
            if (userGuess === secretNumber) {
                messageDisplay.textContent = `¡Felicidades! ¡Adivinaste el número ${secretNumber} en ${attempts} intentos!`;
                messageDisplay.style.color = '#4ade80'; // Verde para éxito
                gameOver = true; // Marcar el juego como terminado
                guessInput.disabled = true; // Deshabilitar el input
                checkButton.disabled = true; // Deshabilitar el botón de comprobar
                resetButton.classList.remove('hidden'); // Mostrar el botón de reinicio
            } else if (userGuess < secretNumber) {
                messageDisplay.textContent = 'El número es más alto. ¡Intenta de nuevo!';
                messageDisplay.style.color = '#fcd34d'; // Amarillo para pista
            } else {
                messageDisplay.textContent = 'El número es más bajo. ¡Intenta de nuevo!';
                messageDisplay.style.color = '#fcd34d'; // Amarillo para pista
            }

            guessInput.value = ''; // Limpiar el campo de entrada después de cada intento
            guessInput.focus(); // Mantener el foco en el campo de entrada
        }

        // Event Listeners
        // Al hacer clic en el botón "Comprobar"
        checkButton.addEventListener('click', checkGuess);

        // Al presionar "Enter" en el campo de entrada
        guessInput.addEventListener('keypress', function(event) {
            if (event.key === 'Enter') {
                checkGuess();
            }
        });

        // Al hacer clic en el botón "Reiniciar Juego"
        resetButton.addEventListener('click', initializeGame);

        // Inicializar el juego cuando la página se carga
        window.onload = initializeGame;
    </script>
</body>
</html>
