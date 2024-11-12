<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Survival Storm</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #1c1c1c;
            color: white;
            text-align: center;
        }
        .hidden {
            display: none;
        }
        #gameCanvas {
            border: 1px solid white;
            width: 100%;
            height: 600px;
            background-color: #333;
            display: block;
            margin: 0 auto;
        }
        .menu, .gameUI {
            display: none;
        }
        .menu.active, .gameUI.active {
            display: block;
        }
        #characterSelection {
            display: flex;
            justify-content: center;
            margin-top: 20px;
        }
        .character {
            margin: 0 15px;
            cursor: pointer;
        }
        .character img {
            width: 100px;
            height: 100px;
        }
        #preGameInfo {
            margin-top: 20px;
        }
        .button {
            padding: 10px 20px;
            background-color: #007BFF;
            color: white;
            border: none;
            cursor: pointer;
            font-size: 16px;
        }
        .button:hover {
            background-color: #0056b3;
        }
        #endGame {
            margin-top: 30px;
        }
        #scoreboard {
            margin-top: 10px;
            font-size: 18px;
        }
    </style>
</head>
<body>

    <div class="menu active" id="mainMenu">
        <h1>Survival Storm</h1>
        <button class="button" id="playButton">Play</button>
        <button class="button" id="settingsButton">Settings</button>
        <button class="button" id="leaderboardsButton">Leaderboards</button>
    </div>

    <div class="menu" id="characterSelection">
        <h2>Select Your Character</h2>
        <div class="character" onclick="selectCharacter('Warrior')">
            <img src="https://via.placeholder.com/100" alt="Warrior">
            <p>Warrior</p>
        </div>
        <div class="character" onclick="selectCharacter('Sniper')">
            <img src="https://via.placeholder.com/100" alt="Sniper">
            <p>Sniper</p>
        </div>
        <div class="character" onclick="selectCharacter('Medic')">
            <img src="https://via.placeholder.com/100" alt="Medic">
            <p>Medic</p>
        </div>
        <button class="button" id="startGameButton">Start Game</button>
    </div>

    <div class="gameUI hidden" id="gameUI">
        <h2>Game In Progress</h2>
        <p id="preGameInfo">Preparing for airdrop...</p>
        <canvas id="gameCanvas"></canvas>
        <div id="scoreboard">
            <p><strong>Player:</strong> <span id="playerName">Player1</span></p>
            <p><strong>Health:</strong> <span id="health">100</span></p>
            <p><strong>Ammo:</strong> <span id="ammo">50</span></p>
        </div>
    </div>

    <div id="endGame" class="hidden">
        <h2>Game Over!</h2>
        <p id="endGameMessage"></p>
        <button class="button" onclick="restartGame()">Play Again</button>
    </div>

    <script>
        // Main menu events
        document.getElementById('playButton').onclick = function() {
            document.getElementById('mainMenu').classList.remove('active');
            document.getElementById('characterSelection').classList.add('active');
        };

        // Character selection
        let selectedCharacter = null;
        function selectCharacter(character) {
            selectedCharacter = character;
            document.getElementById('preGameInfo').innerText = `You have selected: ${character}`;
        }

        document.getElementById('startGameButton').onclick = function() {
            if (selectedCharacter) {
                startGame();
            } else {
                alert("Please select a character first!");
            }
        };

        // Start the game
        function startGame() {
            document.getElementById('characterSelection').classList.remove('active');
            document.getElementById('gameUI').classList.add('active');
            document.getElementById('preGameInfo').innerText = "Jumping out of the plane... Get ready!";
            
            // Simulate gameplay with a simple timer
            setTimeout(() => {
                document.getElementById('preGameInfo').innerText = "You have landed. Start scavenging!";
                simulateGameplay();
            }, 2000);  // simulate 2 second delay for the airdrop

            // Initialize canvas for gameplay
            initCanvas();
        }

        // Game Variables
        let canvas = document.getElementById('gameCanvas');
        let ctx = canvas.getContext('2d');
        let player = { x: 200, y: 50, width: 50, height: 50, color: 'blue' };
        let parachute = { x: 200, y: 50, width: 80, height: 30, color: 'white' };

        // Initialize the canvas
        function initCanvas() {
            canvas.width = window.innerWidth - 40;
            canvas.height = 600;

            // Draw the background (simple gradient for now)
            ctx.fillStyle = '#0099CC'; // sky blue color
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            // Draw the island (a simple green rectangle as placeholder)
            ctx.fillStyle = '#33cc33'; // island green
            ctx.fillRect(0, canvas.height - 100, canvas.width, 100); // island base

            // Draw player character (as a blue square for now)
            drawParachute();
        }

        // Draw the parachute and player
        function drawParachute() {
            // Parachute animation effect (falling down)
            parachute.y += 5; // simulate falling

            // Clear canvas before redrawing
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Redraw background and island
            ctx.fillStyle = '#0099CC'; // sky blue
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            ctx.fillStyle = '#33cc33'; // island green
            ctx.fillRect(0, canvas.height - 100, canvas.width, 100);

            // Draw parachute
            ctx.fillStyle = parachute.color;
            ctx.fillRect(parachute.x - 40, parachute.y, parachute.width, parachute.height); // parachute

            // Draw the player (blue square)
            ctx.fillStyle = player.color;
            ctx.fillRect(player.x, parachute.y + parachute.height, player.width, player.height); // player below parachute

            // Check if player has landed (i.e., parachute reaches island level)
            if (parachute.y + parachute.height >= canvas.height - 100) {
                document.getElementById('preGameInfo').innerText = "You have landed!";
                endGame("Game Over. You have landed safely.");
            } else {
                requestAnimationFrame(drawParachute); // keep animating
            }
        }

        // End the game
        function endGame(message) {
            document.getElementById('gameUI').classList.remove('active');
            document.getElementById('endGame').classList.add('active');
            document.getElementById('endGameMessage').innerText = message;
        }

        // Restart the game
        function restartGame() {
            document.getElementById('endGame').classList.remove('active');
            document.getElementById('mainMenu').classList.add('active');
        }
    </script>

</body>
</html><!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Survival Storm</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #1c1c1c;
            color: white;
            text-align: center;
        }
        .hidden {
            display: none;
        }
        #gameCanvas {
            border: 1px solid white;
            width: 100%;
            height: 600px;
            background-color: #333;
            display: block;
            margin: 0 auto;
        }
        .menu, .gameUI {
            display: none;
        }
        .menu.active, .gameUI.active {
            display: block;
        }
        #characterSelection {
            display: flex;
            justify-content: center;
            margin-top: 20px;
        }
        .character {
            margin: 0 15px;
            cursor: pointer;
        }
        .character img {
            width: 100px;
            height: 100px;
        }
        #preGameInfo {
            margin-top: 20px;
        }
        .button {
            padding: 10px 20px;
            background-color: #007BFF;
            color: white;
            border: none;
            cursor: pointer;
            font-size: 16px;
        }
        .button:hover {
            background-color: #0056b3;
        }
        #endGame {
            margin-top: 30px;
        }
        #scoreboard {
            margin-top: 10px;
            font-size: 18px;
        }
    </style>
</head>
<body>

    <div class="menu active" id="mainMenu">
        <h1>Survival Storm</h1>
        <button class="button" id="playButton">Play</button>
        <button class="button" id="settingsButton">Settings</button>
        <button class="button" id="leaderboardsButton">Leaderboards</button>
    </div>

    <div class="menu" id="characterSelection">
        <h2>Select Your Character</h2>
        <div class="character" onclick="selectCharacter('Warrior')">
            <img src="https://via.placeholder.com/100" alt="Warrior">
            <p>Warrior</p>
        </div>
        <div class="character" onclick="selectCharacter('Sniper')">
            <img src="https://via.placeholder.com/100" alt="Sniper">
            <p>Sniper</p>
        </div>
        <div class="character" onclick="selectCharacter('Medic')">
            <img src="https://via.placeholder.com/100" alt="Medic">
            <p>Medic</p>
        </div>
        <button class="button" id="startGameButton">Start Game</button>
    </div>

    <div class="gameUI hidden" id="gameUI">
        <h2>Game In Progress</h2>
        <p id="preGameInfo">Preparing for airdrop...</p>
        <canvas id="gameCanvas"></canvas>
        <div id="scoreboard">
            <p><strong>Player:</strong> <span id="playerName">Player1</span></p>
            <p><strong>Health:</strong> <span id="health">100</span></p>
            <p><strong>Ammo:</strong> <span id="ammo">50</span></p>
        </div>
    </div>

    <div id="endGame" class="hidden">
        <h2>Game Over!</h2>
        <p id="endGameMessage"></p>
        <button class="button" onclick="restartGame()">Play Again</button>
    </div>

    <script>
        // Main menu events
        document.getElementById('playButton').onclick = function() {
            document.getElementById('mainMenu').classList.remove('active');
            document.getElementById('characterSelection').classList.add('active');
        };

        // Character selection
        let selectedCharacter = null;
        function selectCharacter(character) {
            selectedCharacter = character;
            document.getElementById('preGameInfo').innerText = `You have selected: ${character}`;
        }

        document.getElementById('startGameButton').onclick = function() {
            if (selectedCharacter) {
                startGame();
            } else {
                alert("Please select a character first!");
            }
        };

        // Start the game
        function startGame() {
            document.getElementById('characterSelection').classList.remove('active');
            document.getElementById('gameUI').classList.add('active');
            document.getElementById('preGameInfo').innerText = "Jumping out of the plane... Get ready!";
            
            // Simulate gameplay with a simple timer
            setTimeout(() => {
                document.getElementById('preGameInfo').innerText = "You have landed. Start scavenging!";
                simulateGameplay();
            }, 2000);  // simulate 2 second delay for the airdrop

            // Initialize canvas for gameplay
            initCanvas();
        }

        // Game Variables
        let canvas = document.getElementById('gameCanvas');
        let ctx = canvas.getContext('2d');
        let player = { x: 200, y: 50, width: 50, height: 50, color: 'blue' };
        let parachute = { x: 200, y: 50, width: 80, height: 30, color: 'white' };

        // Initialize the canvas
        function initCanvas() {
            canvas.width = window.innerWidth - 40;
            canvas.height = 600;

            // Draw the background (simple gradient for now)
            ctx.fillStyle = '#0099CC'; // sky blue color
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            // Draw the island (a simple green rectangle as placeholder)
            ctx.fillStyle = '#33cc33'; // island green
            ctx.fillRect(0, canvas.height - 100, canvas.width, 100); // island base

            // Draw player character (as a blue square for now)
            drawParachute();
        }

        // Draw the parachute and player
        function drawParachute() {
            // Parachute animation effect (falling down)
            parachute.y += 5; // simulate falling

            // Clear canvas before redrawing
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Redraw background and island
            ctx.fillStyle = '#0099CC'; // sky blue
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            ctx.fillStyle = '#33cc33'; // island green
            ctx.fillRect(0, canvas.height - 100, canvas.width, 100);

            // Draw parachute
            ctx.fillStyle = parachute.color;
            ctx.fillRect(parachute.x - 40, parachute.y, parachute.width, parachute.height); // parachute

            /
