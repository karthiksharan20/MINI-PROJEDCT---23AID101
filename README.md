<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Animal Hangman</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Poppins:wght@400;600;700&display=swap');
        
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Poppins', sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 50%, #f093fb 100%);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 20px;
            position: relative;
            overflow: hidden;
        }

        body::before {
            content: '';
            position: absolute;
            width: 500px;
            height: 500px;
            background: rgba(255, 255, 255, 0.1);
            border-radius: 50%;
            top: -250px;
            left: -250px;
            animation: float 20s infinite ease-in-out;
        }

        body::after {
            content: '';
            position: absolute;
            width: 400px;
            height: 400px;
            background: rgba(255, 255, 255, 0.08);
            border-radius: 50%;
            bottom: -200px;
            right: -200px;
            animation: float 25s infinite ease-in-out reverse;
        }

        @keyframes float {
            0%, 100% { transform: translate(0, 0) rotate(0deg); }
            33% { transform: translate(30px, -50px) rotate(120deg); }
            66% { transform: translate(-20px, 20px) rotate(240deg); }
        }

        .container {
            background: rgba(255, 255, 255, 0.95);
            backdrop-filter: blur(10px);
            border-radius: 30px;
            box-shadow: 0 30px 80px rgba(0, 0, 0, 0.3);
            padding: 50px;
            max-width: 950px;
            width: 100%;
            animation: slideIn 0.8s cubic-bezier(0.34, 1.56, 0.64, 1);
            position: relative;
            z-index: 10;
            border: 2px solid rgba(255, 255, 255, 0.3);
        }

        @keyframes slideIn {
            from {
                opacity: 0;
                transform: translateY(-50px) scale(0.9);
            }
            to {
                opacity: 1;
                transform: translateY(0) scale(1);
            }
        }

        h1 {
            text-align: center;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
            margin-bottom: 40px;
            font-size: 3em;
            font-weight: 700;
            text-shadow: 2px 2px 20px rgba(102, 126, 234, 0.3);
            letter-spacing: 2px;
        }

        .game-info {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 35px;
            padding: 25px 30px;
            background: linear-gradient(135deg, #f8f9fa 0%, #e9ecef 100%);
            border-radius: 20px;
            box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
            border: 2px solid rgba(102, 126, 234, 0.2);
        }

        .lives {
            font-size: 1.6em;
            font-weight: 700;
            color: #e74c3c;
            transition: all 0.4s;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.1);
        }

        .lives.safe {
            color: #27ae60;
        }

        .lives.warning {
            color: #f39c12;
            animation: pulse 1s infinite;
        }

        @keyframes pulse {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(1.05); }
        }

        .hangman-display {
            text-align: center;
            margin: 35px 0;
            background: linear-gradient(135deg, #2c3e50 0%, #34495e 100%);
            padding: 40px;
            border-radius: 20px;
            font-family: 'Courier New', monospace;
            font-size: 1.3em;
            white-space: pre;
            line-height: 1.4;
            color: #ecf0f1;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3), inset 0 2px 10px rgba(0, 0, 0, 0.2);
            border: 3px solid rgba(236, 240, 241, 0.1);
        }

        .word-display {
            text-align: center;
            font-size: 3.2em;
            letter-spacing: 20px;
            margin: 40px 0;
            font-weight: 700;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
            font-family: 'Poppins', sans-serif;
            min-height: 80px;
            display: flex;
            justify-content: center;
            align-items: center;
            filter: drop-shadow(2px 2px 4px rgba(0, 0, 0, 0.1));
        }

        .letter {
            display: inline-block;
            animation: flipIn 0.6s cubic-bezier(0.34, 1.56, 0.64, 1);
        }

        @keyframes flipIn {
            from {
                transform: rotateY(90deg) scale(0.5);
                opacity: 0;
            }
            to {
                transform: rotateY(0) scale(1);
                opacity: 1;
            }
        }

        .message {
            text-align: center;
            margin: 25px 0;
            font-size: 1.3em;
            font-weight: 600;
            min-height: 35px;
            color: #7f8c8d;
            transition: all 0.3s;
        }

        .message.error {
            color: #e74c3c;
            animation: shake 0.6s;
        }

        .message.success {
            color: #27ae60;
            animation: bounce 0.6s;
        }

        @keyframes shake {
            0%, 100% { transform: translateX(0); }
            25% { transform: translateX(-15px); }
            75% { transform: translateX(15px); }
        }

        @keyframes bounce {
            0%, 100% { transform: translateY(0); }
            50% { transform: translateY(-10px); }
        }

        .keyboard {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(55px, 1fr));
            gap: 12px;
            margin: 35px 0;
            max-width: 750px;
            margin-left: auto;
            margin-right: auto;
        }

        .key {
            padding: 20px;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            border: none;
            border-radius: 15px;
            font-size: 1.3em;
            font-weight: 700;
            cursor: pointer;
            transition: all 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
            text-transform: uppercase;
            box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
            position: relative;
            overflow: hidden;
        }

        .key::before {
            content: '';
            position: absolute;
            top: 50%;
            left: 50%;
            width: 0;
            height: 0;
            border-radius: 50%;
            background: rgba(255, 255, 255, 0.3);
            transform: translate(-50%, -50%);
            transition: width 0.6s, height 0.6s;
        }

        .key:hover::before {
            width: 300px;
            height: 300px;
        }

        .key:hover:not(:disabled) {
            transform: translateY(-5px) scale(1.05);
            box-shadow: 0 8px 20px rgba(102, 126, 234, 0.6);
        }

        .key:active:not(:disabled) {
            transform: translateY(-2px) scale(1.02);
        }

        .key:disabled {
            background: linear-gradient(135deg, #bdc3c7 0%, #95a5a6 100%);
            cursor: not-allowed;
            opacity: 0.6;
            transform: scale(0.95);
        }

        .key.correct {
            background: linear-gradient(135deg, #27ae60 0%, #229954 100%);
            box-shadow: 0 5px 15px rgba(39, 174, 96, 0.4);
            animation: correctPulse 0.5s;
        }

        .key.wrong {
            background: linear-gradient(135deg, #e74c3c 0%, #c0392b 100%);
            box-shadow: 0 5px 15px rgba(231, 76, 60, 0.4);
            animation: wrongShake 0.5s;
        }

        @keyframes correctPulse {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(1.15); }
        }

        @keyframes wrongShake {
            0%, 100% { transform: rotate(0deg); }
            25% { transform: rotate(-10deg); }
            75% { transform: rotate(10deg); }
        }

        .game-over-overlay {
            display: none;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.85);
            backdrop-filter: blur(10px);
            justify-content: center;
            align-items: center;
            z-index: 1000;
            animation: fadeIn 0.4s;
        }

        @keyframes fadeIn {
            from { opacity: 0; }
            to { opacity: 1; }
        }

        .game-over-content {
            background: white;
            padding: 60px;
            border-radius: 30px;
            text-align: center;
            animation: scaleIn 0.6s cubic-bezier(0.34, 1.56, 0.64, 1);
            max-width: 550px;
            box-shadow: 0 30px 80px rgba(0, 0, 0, 0.5);
            border: 3px solid rgba(102, 126, 234, 0.3);
        }

        @keyframes scaleIn {
            from {
                transform: scale(0.5) rotate(-5deg);
                opacity: 0;
            }
            to {
                transform: scale(1) rotate(0deg);
                opacity: 1;
            }
        }

        .game-over-content h2 {
            font-size: 3.5em;
            margin-bottom: 25px;
            font-weight: 700;
            text-shadow: 2px 2px 10px rgba(0, 0, 0, 0.1);
        }

        .game-over-content.win h2 {
            background: linear-gradient(135deg, #27ae60 0%, #229954 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
            animation: winAnimation 1s infinite;
        }

        .game-over-content.lose h2 {
            background: linear-gradient(135deg, #e74c3c 0%, #c0392b 100%);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            background-clip: text;
        }

        @keyframes winAnimation {
            0%, 100% { transform: scale(1); }
            50% { transform: scale(1.1); }
        }

        .game-over-content p {
            font-size: 1.4em;
            margin: 25px 0;
            color: #2c3e50;
            font-weight: 500;
        }

        .button-group {
            display: flex;
            gap: 20px;
            justify-content: center;
            margin-top: 35px;
            flex-wrap: wrap;
        }

        .restart-btn {
            padding: 18px 45px;
            font-size: 1.3em;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            border: none;
            border-radius: 15px;
            cursor: pointer;
            font-weight: 700;
            transition: all 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
            box-shadow: 0 5px 20px rgba(102, 126, 234, 0.4);
            font-family: 'Poppins', sans-serif;
        }

        .restart-btn:hover {
            transform: translateY(-3px);
            box-shadow: 0 8px 25px rgba(102, 126, 234, 0.6);
        }

        .restart-btn:active {
            transform: translateY(-1px);
        }

        .close-btn {
            background: linear-gradient(135deg, #e74c3c 0%, #c0392b 100%);
            box-shadow: 0 5px 20px rgba(231, 76, 60, 0.4);
        }

        .close-btn:hover {
            box-shadow: 0 8px 25px rgba(231, 76, 60, 0.6);
        }

        @media (max-width: 768px) {
            .container {
                padding: 30px;
            }

            h1 {
                font-size: 2em;
            }

            .word-display {
                font-size: 2em;
                letter-spacing: 10px;
            }

            .keyboard {
                grid-template-columns: repeat(auto-fit, minmax(45px, 1fr));
            }

            .key {
                padding: 15px;
                font-size: 1.1em;
            }
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>🐾 WELCOME TO ANIMAL HANGMAN 🐾</h1>
        
        <div class="game-info">
            <div class="lives safe" id="lives">❤️ 6/6 LIVES LEFT</div>
        </div>

        <div class="hangman-display" id="hangman">
  +---+
  |   |
      |
      |
      |
      |
=========
        </div>

        <div class="word-display" id="wordDisplay"></div>
        
        <div class="message" id="message"></div>

        <div class="keyboard" id="keyboard"></div>
    </div>

    <div class="game-over-overlay" id="gameOverOverlay">
        <div class="game-over-content" id="gameOverContent">
            <h2 id="gameOverTitle"></h2>
            <p id="gameOverMessage"></p>
            <div class="button-group">
                <button class="restart-btn" onclick="restartGame()">🎮 Play Again</button>
                <button class="restart-btn close-btn" onclick="closeGame()">❌ Close Game</button>
            </div>
        </div>
    </div>

    <script>
        const wordList = ["buffalo", "baboon", "camel", "dog", "cat", "butterfly", "capybara", "cow", "pig", "duck", "bear", "lion", "tiger", "mouse", "rat", "frog", "sheep", "goat", "horse", "zebra", "monkey", "rabbit", "chicken"];
        
        const stages = [
`  +---+
  |   |
  O   |
 /|\\  |
 / \\  |
      |
=========`,
`  +---+
  |   |
  O   |
 /|\\  |
 /    |
      |
=========`,
`  +---+
  |   |
  O   |
 /|\\  |
      |
      |
=========`,
`  +---+
  |   |
  O   |
 /|   |
      |
      |
=========`,
`  +---+
  |   |
  O   |
  |   |
      |
      |
=========`,
`  +---+
  |   |
  O   |
      |
      |
      |
=========`,
`  +---+
  |   |
      |
      |
      |
      |
=========`
        ];

        let chosenWord = "";
        let lives = 6;
        let correctLetters = [];
        let guessedLetters = [];
        let gameOver = false;

        function initGame() {
            chosenWord = wordList[Math.floor(Math.random() * wordList.length)].toLowerCase();
            lives = 6;
            correctLetters = [];
            guessedLetters = [];
            gameOver = false;
            
            updateDisplay();
            createKeyboard();
            updateHangman();
            document.getElementById('message').textContent = '';
            document.getElementById('gameOverOverlay').style.display = 'none';
        }

        function createKeyboard() {
            const keyboard = document.getElementById('keyboard');
            keyboard.innerHTML = '';
            
            for (let i = 97; i <= 122; i++) {
                const letter = String.fromCharCode(i);
                const button = document.createElement('button');
                button.className = 'key';
                button.textContent = letter;
                button.onclick = () => guessLetter(letter, button);
                keyboard.appendChild(button);
            }
        }

        function guessLetter(letter, button) {
            if (gameOver || guessedLetters.includes(letter)) {
                if (guessedLetters.includes(letter)) {
                    showMessage(`You already guessed "${letter}"!`, 'error');
                }
                return;
            }

            guessedLetters.push(letter);
            button.disabled = true;

            if (chosenWord.includes(letter)) {
                correctLetters.push(letter);
                button.classList.add('correct');
                showMessage(`Great! "${letter}" is in the word!`, 'success');
            } else {
                lives--;
                button.classList.add('wrong');
                showMessage(`Sorry! "${letter}" is not in the word.`, 'error');
                updateHangman();
            }

            updateDisplay();
            checkGameOver();
        }

        function updateDisplay() {
            let display = '';
            for (let letter of chosenWord) {
                if (correctLetters.includes(letter)) {
                    display += letter + ' ';
                } else {
                    display += '_ ';
                }
            }
            
            document.getElementById('wordDisplay').innerHTML = display.trim().split('').map(char => 
                `<span class="letter">${char}</span>`
            ).join('');

            const livesElement = document.getElementById('lives');
            livesElement.textContent = `❤️ ${lives}/6 LIVES LEFT`;
            
            if (lives <= 2) {
                livesElement.className = 'lives warning';
            } else if (lives <= 4) {
                livesElement.className = 'lives warning';
            } else {
                livesElement.className = 'lives safe';
            }
        }

        function updateHangman() {
            document.getElementById('hangman').textContent = stages[lives];
        }

        function showMessage(text, type) {
            const messageEl = document.getElementById('message');
            messageEl.textContent = text;
            messageEl.className = 'message ' + type;
            
            setTimeout(() => {
                messageEl.className = 'message';
            }, 2000);
        }

        function checkGameOver() {
            const wordComplete = chosenWord.split('').every(letter => correctLetters.includes(letter));
            
            if (wordComplete) {
                gameOver = true;
                setTimeout(() => showGameOver(true), 500);
            } else if (lives === 0) {
                gameOver = true;
                setTimeout(() => showGameOver(false), 500);
            }
        }

        function showGameOver(won) {
            const overlay = document.getElementById('gameOverOverlay');
            const content = document.getElementById('gameOverContent');
            const title = document.getElementById('gameOverTitle');
            const message = document.getElementById('gameOverMessage');

            if (won) {
                content.className = 'game-over-content win';
                title.textContent = '🎉 YOU WIN! 🎉';
                message.textContent = `Congratulations! You guessed the word "${chosenWord}"!`;
            } else {
                content.className = 'game-over-content lose';
                title.textContent = '💀 YOU LOSE 💀';
                message.textContent = `Game Over! The word was "${chosenWord}".`;
            }

            overlay.style.display = 'flex';
        }

        function restartGame() {
            initGame();
        }

        function closeGame() {
            if (confirm('Are you sure you want to close the game?')) {
                window.close();
                setTimeout(() => {
                    document.body.innerHTML = '<div style="display: flex; justify-content: center; align-items: center; height: 100vh; font-size: 2em; color: white; text-align: center; font-family: Poppins, sans-serif;">Thanks for playing Animal Hangman! 🐾<br><br>You can close this tab now.</div>';
                }, 100);
            }
        }

        document.addEventListener('keydown', (e) => {
            if (gameOver) return;
            
            const key = e.key.toLowerCase();
            if (key >= 'a' && key <= 'z') {
                const buttons = document.querySelectorAll('.key');
                buttons.forEach(button => {
                    if (button.textContent === key && !button.disabled) {
                        button.click();
                    }
                });
            }
        });

        initGame();
    </script>
</body>
</html>

Restart: A game over screen will appear, allowing you to quickly start a new game.
