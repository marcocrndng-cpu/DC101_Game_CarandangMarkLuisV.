# DC101_Game_CarandangMarkLuisV.
This is a game using a mind because it it matching fruit game
!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Memory Game</title>
<link rel="stylesheet" href="style.css" />
</head>
<body>

<h1>Memory Game</h1>
<div id="timer">Time Left: 05:00</div>
<div id="game-board"></div>

<div id="resultModal">
  <div class="modal-content">
    <div class="checkmark">✔️</div>
    <h2 id="modalTitle">Congratulations! You Won!</h2>
    <p id="modalText"></p>
    <button onclick="restartGame()">Play again!</button>
  </div>
</div>


<script src="main.js"></script>
</body>
</html>

body {
      font-family: Arial, sans-serif;
      text-align: center;
      background: #f4f4f9;
    }
    h1 { margin: 20px 0; }

    #timer { font-size: 20px; margin-bottom: 10px; }

    #game-board {
      display: grid;
      grid-template-columns: repeat(4, 100px);
      grid-gap: 10px;
      justify-content: center;
      margin: 20px auto;
    }

    .card {
      width: 100px;
      height: 100px;
      background: #3498db;
      color: #fff;
      font-size: 40px;
      display: flex;
      justify-content: center;
      align-items: center;
      cursor: pointer;
      border-radius: 10px;
      user-select: none;
    }

    .card.flipped {
      background: #2ecc71;
      cursor: default;
    }

    #resultModal {
      display: none;
      position: fixed;
      z-index: 10;
      left: 0; top: 0;
      width: 100%; height: 100%;
      background: rgba(0,0,0,0.6);
      justify-content: center;
      align-items: center;
    }

    .modal-content {
      background: #fff;
      padding: 30px;
      border-radius: 10px;
      text-align: center;
      max-width: 350px;
      width: 90%;
      box-shadow: 0px 5px 15px rgba(0,0,0,0.3);
    }

    .modal-content h2 {
      margin: 15px 0;
      color: #333;
    }

    .checkmark {
      font-size: 60px;
      color: green;
    }

    .modal-content button {
      margin-top: 15px;
      padding: 10px 20px;
      background: #7ed957;
      border: none;
      border-radius: 5px;
      color: white;
      font-size: 16px;
      cursor: pointer;
    }
    .modal-content button:hover {
      background: #5cc94f;
    }

    const board = document.getElementById("game-board");
  const timerEl = document.getElementById("timer");
  const modal = document.getElementById("resultModal");
  const modalTitle = document.getElementById("modalTitle");
  const modalText = document.getElementById("modalText");

  let cardsArray = ["🍎","🍌","🍓","🍇","🍒","🍋","🍍","🥝"];
  let cards, firstCard, secondCard, lockBoard, matched, moves, timer, timeLeft;

  function initGame() {
    board.innerHTML = "";
    cards = [...cardsArray, ...cardsArray];
    cards.sort(() => 0.5 - Math.random());
    firstCard = secondCard = null;
    lockBoard = false;
    matched = 0;
    moves = 0;
    timeLeft = 300;

    cards.forEach((emoji) => {
      const card = document.createElement("div");
      card.classList.add("card");
      card.dataset.value = emoji;
      card.textContent = "?";
      board.appendChild(card);
      card.addEventListener("click", () => flipCard(card));
    });

    clearInterval(timer);
    timer = setInterval(updateTimer, 1000);
  }

  function flipCard(card) {
    if (lockBoard || card.classList.contains("flipped")) return;
    card.textContent = card.dataset.value;
    if (!firstCard) {
      firstCard = card;
    } else {
      secondCard = card;
      moves++;
      checkMatch();
    }
  }

  function checkMatch() {
    if (firstCard.dataset.value === secondCard.dataset.value) {
      firstCard.classList.add("flipped");
      secondCard.classList.add("flipped");
      matched += 2;
      resetBoard();
      if (matched === cards.length) endGame(true);
    } else {
      lockBoard = true;
      setTimeout(() => {
        firstCard.textContent = "?";
        secondCard.textContent = "?";
        resetBoard();
      }, 800);
    }
  }

  function resetBoard() {
    [firstCard, secondCard, lockBoard] = [null, null, false];
  }

  function updateTimer() {
    let minutes = Math.floor(timeLeft / 60);
    let seconds = timeLeft % 60;
    timerEl.textContent = `Time Left: ${minutes.toString().padStart(2,"0")}:${seconds.toString().padStart(2,"0")}`;
    timeLeft--;
    if (timeLeft < 0) {
      clearInterval(timer);
      endGame(false);
    }
  }

  function endGame(completed) {
    clearInterval(timer);
    let rating = getRating();
    modal.style.display = "flex";
    if (completed) {
      modalTitle.textContent = "Congratulations! You Won!";
      modalText.textContent = `With ${moves} Moves and ${rating} Stars. 🎉`;
    } else {
      modalTitle.textContent = "Time's Up!";
      modalText.textContent = `With ${moves} Moves and ${rating} Stars.`;
    }
  }

  function getRating() {
    const minMoves = cards.length / 2; 
    if (moves <= minMoves) return 5;
    else if (moves <= minMoves + 2) return 4;
    else if (moves <= minMoves + 4) return 3;
    else if (moves <= minMoves + 6) return 2;
    else return 1;
  }

  function restartGame() {
    modal.style.display = "none";
    initGame();
  }


  initGame();
