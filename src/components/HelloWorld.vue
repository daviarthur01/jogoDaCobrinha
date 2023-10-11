<template>
  <div class="game-container">
    <canvas id="gameCanvas" width="400" height="400"></canvas>
    <div class="controls">
      <table class="cross-controls">
        <tr>
          <td></td>
          <td><button id="upButton" @click="moveUp">↑</button></td>
          <td></td>
        </tr>
        <tr>
          <td><button id="leftButton" @click="moveLeft">←</button></td>
          <td></td>
          <td><button id="rightButton" @click="moveRight">→</button></td>
        </tr>
        <tr>
          <td></td>
          <td><button id="downButton" @click="moveDown">↓</button></td>
          <td></td>
        </tr>
      </table>
    </div>
    <div class="controls">
      <button id="startButton" @click="startGame">Start</button>
      <button id="pauseButton" @click="pauseGame">Pause</button>
      <button id="restartButton" @click="restartGame">Restart</button>
    </div>
    <div class="score">
      Score: {{ score }}
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
.game-container {
  position: relative;
  text-align: center;
}

.controls {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-top: 10px;
}

.cross-controls {
  border-collapse: collapse;
}

.cross-controls td {
  text-align: center;
}

.cross-controls button {
  font-size: 24px;
  padding: 5px 10px;
  margin: 5px;
}

#gameCanvas {
  width: 313px;
  height: 368px;
  border: 2px solid #000;
}

.score {
  font-size: 20px;
}
</style>
