<template>
  <div class="game-container">
    <canvas id="gameCanvas" width="400" height="400"></canvas>
    <div class="controls">
      <button id="startButton" @click="startGame">Start</button>
      <button id="pauseButton" @click="pauseGame">Pause</button>
    </div>
    <div class="controls">
      <table class="cross-controls">
        <tr>
          <td></td>
          <td><button id="upButton" class="button" @click="moveUp">↑</button></td>
          <td></td>
        </tr>
        <tr>
          <td><button id="leftButton" class="button" @click="moveLeft">←</button></td>
          <td></td>
          <td><button id="rightButton" class="button" @click="moveRight">→</button></td>
        </tr>
        <tr>
          <td></td>
          <td><button id="downButton" class="button" @click="moveDown">↓</button></td>
          <td></td>
        </tr>
      </table>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      canvas: null,
      ctx: null,
      tileSize: 20,
      snake: [{ x: 10, y: 10 }],
      food: { x: 5, y: 5 },
      dx: 0,
      dy: -1,
      score: 0,
      gameInterval: null,
      gameStarted: false,
      gamePaused: false,
    };
  },
  mounted() {
    this.canvas = document.getElementById("gameCanvas");
    this.ctx = this.canvas.getContext("2d");
    this.startGame();
  },
  methods: {
    startGame() {
      if (!this.gameStarted) {
        this.gameStarted = true;
        this.gameInterval = setInterval(this.updateGame, 100);
      } else if (this.gamePaused) {
        this.gamePaused = false;
        this.gameInterval = setInterval(this.updateGame, 100);
      }
    },
    pauseGame() {
      if (this.gameInterval) {
        clearInterval(this.gameInterval);
        this.gamePaused = true;
      }
    },
    restartGame() {
      this.snake = [{ x: 10, y: 10 }];
      this.food = { x: 5, y: 5 };
      this.dx = 0;
      this.dy = -1;
      this.score = 0;
      this.gameStarted = false;
      this.gamePaused = false;
      clearInterval(this.gameInterval);
      this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);
      this.drawSnake();
      this.drawFood();
      this.drawScore();
    },
    moveUp() {
      if (!this.gamePaused && this.dy !== 1) {
        this.dx = 0;
        this.dy = -1;
      }
    },
    moveDown() {
      if (!this.gamePaused && this.dy !== -1) {
        this.dx = 0;
        this.dy = 1;
      }
    },
    moveLeft() {
      if (!this.gamePaused && this.dx !== 1) {
        this.dx = -1;
        this.dy = 0;
      }
    },
    moveRight() {
      if (!this.gamePaused && this.dx !== -1) {
        this.dx = 1;
        this.dy = 0;
      }
    },
    drawSnake() {
      this.snake.forEach(segment => {
        this.ctx.fillStyle = "green";
        this.ctx.fillRect(
          segment.x * this.tileSize,
          segment.y * this.tileSize,
          this.tileSize,
          this.tileSize
        );
      });
    },
    drawFood() {
      this.ctx.fillStyle = "red";
      this.ctx.fillRect(
        this.food.x * this.tileSize,
        this.food.y * this.tileSize,
        this.tileSize,
        this.tileSize
      );
    },
    drawScore() {
      this.ctx.fillStyle = "black";
      this.ctx.font = "20px Arial";
      this.ctx.fillText("Score: " + this.score, 10, 30);
    },
    checkCollision() {
      const head = this.snake[0];
      if (
        head.x < 0 ||
        head.x >= this.canvas.width / this.tileSize ||
        head.y < 0 ||
        head.y >= this.canvas.height / this.tileSize
      ) {
        return true;
      }
      for (let i = 1; i < this.snake.length; i++) {
        if (
          this.snake[i].x === head.x &&
          this.snake[i].y === head.y
        ) {
          return true;
        }
      }
      return false;
    },
    updateGame() {
      if (this.checkCollision()) {
        alert("Game Over! Score: " + this.score);
        this.restartGame();
        return;
      }

      const newHead = { x: this.snake[0].x + this.dx, y: this.snake[0].y + this.dy };
      this.snake.unshift(newHead);

      if (newHead.x === this.food.x && newHead.y === this.food.y) {
        this.score += 10;
        this.food.x = Math.floor(Math.random() * (this.canvas.width / this.tileSize));
        this.food.y = Math.floor(Math.random() * (this.canvas.height / this.tileSize));
      } else {
        this.snake.pop();
      }

      this.ctx.clearRect(0, 0, this.canvas.width, this.canvas.height);
      this.drawSnake();
      this.drawFood();
      this.drawScore();
    },
    keyDown(event) {
      if (!this.gamePaused) {
        switch (event.key) {
          case "w":
            if (this.dy !== 1) {
              this.dx = 0;
              this.dy = -1;
            }
            break;
          case "s":
            if (this.dy !== -1) {
              this.dx = 0;
              this.dy = 1;
            }
            break;
          case "a":
            if (this.dx !== 1) {
              this.dx = -1;
              this.dy = 0;
            }
            break;
          case "d":
            if (this.dx !== -1) {
              this.dx = 1;
              this.dy = 0;
            }
            break;
        }
      }
    },
  },
  created() {
    document.addEventListener("keydown", this.keyDown);
  },
};
</script>

<style>
.button {

  padding: 19px;
  border-radius: 16px;
  background-color: #81ee85;
  border: transparent;
  box-shadow: 5px 4px 20px 0px;
  font-size: 24px;
  padding: 15px 26px;
  margin: 0px;


}

.button:hover {

  padding: 19px;
  border-radius: 16px;
  background-color: #4caf50;
  border: transparent;
  box-shadow: 5px 4px 20px 0px;
  font-size: 24px;
  padding: 15px 26px;
  margin: 0px;


}

/* <button id="startButton" @click="startGame">Start</button>
      <button id="pauseButton" @click="pauseGame">Pause</button>
      <button id="restartButton" @click="restartGame">Restart</button>*/

#startButton {

  margin: -2px 108px 15px 15px;
  width: 83px;
  height: 27px;
  background: #37df37;
  border: transparent;
  border-radius: 19px;
  box-shadow: 4px 1px 20px 0px;

}

#startButton:hover {

margin: -2px 108px 15px 15px;
width: 83px;
height: 27px;
background: #218f21;
border: transparent;
border-radius: 19px;
box-shadow: 4px 1px 20px 0px;

}

#pauseButton {
  margin: -8px 5px 5px -58px; 
  width: 83px;
  height: 27px;
  background: #f56950;
  border: transparent;
  border-radius: 19px;
  box-shadow: 4px 1px 20px 0px;

}

#pauseButton:hover {
  margin: -8px 5px 5px -58px; 
  width: 83px;
  height: 27px;
  background: #be523f;
  border: transparent;
  border-radius: 19px;
  box-shadow: 4px 1px 20px 0px;

}

.game-container {
  position: relative;
  text-align: center;
}

.controls {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 10px;
  margin: -2px 5px 5px -8px;

}

.cross-controls {
  border-collapse: collapse;
}

.cross-controls td {
  text-align: center;

}

body{

    background: #0000001f;

}

.cross-controls button {
  font-size: 24px;
  padding: 15px 26px;
  margin: 0px;
}

#gameCanvas {
  width: 313px;
  height: 368px;
  border: 2px solid #000;
  margin: -3px 17px 3px 14px;
  background-color: #0000004a;
}

.score {
  font-size: 20px;
}
</style>
