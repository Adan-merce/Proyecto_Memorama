<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Memorama</title>
    <style>
        body {
            text-align: center;
            background-image: url("img/fondo.jpg");
            background-size: cover;
            background-repeat: no-repeat;
            background-attachment: fixed;
            color: white;
        }
        .tablero {
           display: grid;
           grid-template-columns: repeat(4, 160px);
           grid-gap: 10px;
           margin: 20px auto;
        }
        .carta {
            width: 150px;
            height: 150px;
            background-color: #ccc;
            display: flex;
            justify-content: center;
            align-items: center;
            font-size: 2em;
            cursor: pointer;
            user-select: none;
        }
        .carta.oculta {
            background-color: #ccc;
        }
        .carta.oculta img {
            visibility: hidden;
        }
        .carta.encontrada {
            opacity: 0.5; /* Reduce la opacidad de las cartas encontradas */
            pointer-events: none; /* Deshabilita los clics en las cartas encontradas */
        }
        #botonIniciar {
            margin: 20px auto;
            display: block;
            padding: 10px 20px;
            font-size: 1.2em;
            cursor: pointer;
            background-color: #4CAF50;
            color: white;
            border: none;
            border-radius: 5px;
        }
        #tablero-container {
            display: none;
        }
        .carta img {
            width: 150px;
            height: 150px;
        }
        #botones-container {
            margin: 20px auto;
        }
        #botonReiniciar, #botonSalir {
            padding: 10px 20px;
            font-size: 1.2em;
            cursor: pointer;
            border: none;
            border-radius: 5px;
            margin: 0 10px; /* Espacio entre los botones */
        }
        #botonReiniciar {
            background-color: #008CBA;
            color: white;
        }
        #botonSalir {
            background-color: #f44336; /* Color rojo */
            color: white;
        }
        #mensajeCorrecto {
            color: green;
            font-size: 1.5em;
            font-weight: bold;
            margin-top: 10px;
        }
        #inicio-container {
            display: block; /* Asegura que el contenedor de inicio se muestre */
        }
    </style>
</head>
<body>
    <div id="inicio-container">
        <h1>Santiago y Joshua</h1>
        <button id="botonIniciar">Iniciar Juego</button>
    </div>

    <div id="tablero-container">
        <div class="tablero">
            <!-- Las cartas se generarán aquí -->
        </div>
        <p id="mensajeCorrecto"></p>
        <div id="botones-container">
            <button id="botonReiniciar">Reiniciar Juego</button>
            <button id="botonSalir">Salir</button>
        </div>
    </div>

    <script>
        const botonIniciar = document.getElementById('botonIniciar');
        const tableroContainer = document.getElementById('tablero-container');
        const tablero = document.querySelector('.tablero');
        const botonReiniciar = document.getElementById('botonReiniciar');
        const botonSalir = document.getElementById('botonSalir');
        const inicioContainer = document.getElementById('inicio-container');
        let cartas = [
            "img/img1.png",
            "img/img2.png",
            "img/img3.png",
            "img/img4.png",
            "img/img5.png",
            "img/img6.png",
            "img/img7.png",
            "img/img8.png",
            "img/img1.png",
            "img/img2.png",
            "img/img3.png",
            "img/img4.png",
            "img/img5.png",
            "img/img6.png",
            "img/img7.png",
            "img/img8.png"
        ];
        let cartasVolteadas = [];
        let bloqueoTablero = false;
        let cartasEncontradas = 0;

        function barajarCartas() {
            for (let i = cartas.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [cartas[i], cartas[j]] = [cartas[j], cartas[i]];
            }
        }

        function crearTablero() {
            tablero.innerHTML = '';
            barajarCartas();
            cartas.forEach((carta, index) => {
                const elementoCarta = document.createElement('div');
                elementoCarta.classList.add('carta', 'oculta');
                elementoCarta.dataset.id = index;
                const imgElement = document.createElement('img');
                imgElement.src = carta;
                imgElement.alt = "Carta";
                elementoCarta.appendChild(imgElement);
                elementoCarta.addEventListener('click', voltearCarta);
                tablero.appendChild(elementoCarta);
            });
            cartasEncontradas = 0;
        }

        function voltearCarta() {
            if (bloqueoTablero) return;
            if (this === cartasVolteadas[0]) return;

            this.classList.remove('oculta');
            cartasVolteadas.push(this);

            if (cartasVolteadas.length === 2) {
                bloqueoTablero = true;
                setTimeout(comprobarPareja, 500);
            }
        }

        function comprobarPareja() {
            const carta1 = cartasVolteadas[0].querySelector('img').src;
            const carta2 = cartasVolteadas[1].querySelector('img').src;
            const mensajeCorrecto = document.getElementById('mensajeCorrecto');

            if (carta1 === carta2) {
                cartasVolteadas.forEach(carta => {
                    carta.removeEventListener('click', voltearCarta);
                    carta.classList.add('encontrada');
                });
                cartasEncontradas += 2;
                mensajeCorrecto.textContent = "¡Correcto!";
                setTimeout(() => {
                    mensajeCorrecto.textContent = "";
                }, 1000);

                if (cartasEncontradas === cartas.length) {
                    alert("¡Felicidades! Has encontrado todos los pares.");
                }

            } else {
                cartasVolteadas.forEach(carta => carta.classList.add('oculta'));
                mensajeCorrecto.textContent = "";
            }

            cartasVolteadas = [];
            bloqueoTablero = false;
        }

        function resetearJuego() {
            tableroContainer.style.display = 'none';
            inicioContainer.style.display = 'block';
            botonIniciar.style.display = 'block'; // Asegura que el botón Iniciar se muestre
            tablero.innerHTML = ''; // Limpia las cartas del tablero
            cartasVolteadas = [];
            bloqueoTablero = false;
            cartasEncontradas = 0;
        }

        // Oculta el tablero al inicio
        tableroContainer.style.display = 'none';

        botonIniciar.addEventListener('click', function() {
            inicioContainer.style.display = 'none';
            tableroContainer.style.display = 'grid';
            crearTablero();
        });

        botonReiniciar.addEventListener('click', function() {
            crearTablero();
            tableroContainer.style.display = 'grid';
        });

        botonSalir.addEventListener('click', function() {
            resetearJuego();
        });
    </script>
</body>
</html>
