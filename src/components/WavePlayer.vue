<template>
  <div class="player-container">
    <div class="stats">
      <div class="stat-detail">spawned: {{ spawnedEnemies }}</div>
      <div class="stat-detail">killed: {{ killedEnemies }}</div>
      <div class="stat-detail">on screen: {{ spawnedEnemies - killedEnemies }}</div>
    </div>
    <canvas id="canvas" image-rendering="pixelated"></canvas>
    <div class="action-container">
      <b-button class="add-button" variant="success" @click="playWave"
        >Play</b-button
      >
      <b-button class="add-button" variant="danger" @click="setStop(true)"
        >Stop</b-button
      >
      <b-form-select
        class="size-max"
        v-model="playSpeed"
        :options="[1, 2, 4, 8]"
      ></b-form-select>
    </div>
  </div>
</template>

<script>
export default {
  name: "EditableTable",
  data() {
    return {
      playSpeed: 1,
      vueCanvas: null,
      canvas: null,
      angleHashMap: new Map([
        [0, [1000, 0]],
        [15, [1428, 0]],
        [30, [1923, 0]],
        [45, [2000, 600]],
        [60, [2000, 1022]],
        [75, [2000, 1332]],
        [90, [2000, 1600]],
        [105, [2000, 1868]],
        [120, [2000, 2177]],
        [135, [2000, 2600]],
        [150, [1923, 3200]],
        [165, [1428, 3200]],
        [180, [1000, 3200]],
        [195, [571, 3200]],
        [210, [76, 3200]],
        [225, [0, 2600]],
        [240, [0, 2177]],
        [255, [0, 1867]],
        [270, [0, 1600]],
        [285, [0, 1332]],
        [300, [0, 1022]],
        [315, [0, 600]],
        [330, [76, 0]],
        [345, [571, 0]],
        [360, [1000, 0]],
      ]),
      zombieHashMap: new Map([
        ["z1", null],
        ["z2", null],
        ["z3", null],
        ["z4", null],
        ["z5", null],
        ["z6", null],
        ["z7", null],
        ["z8", null],
        ["b1", null],
      ]),
      townCenter: null,
      stackCounter: 0,
      enemyStack: [],
      numberOfEnemies: 0,
      lastStep: 0,
      canvasCenter: {
        x: 1000,
        y: 1600,
      },
      maxEnemiesOnScreen: 150,
      spawnedEnemies: 0,
      killedEnemies: 0,
      isStopped: false,
    };
  },
  components: {},
  mounted() {
    this.canvas = document.getElementById("canvas");
    this.canvas.setAttribute("height", 3200);
    this.canvas.setAttribute("width", 2000);
    let ctx = this.canvas.getContext("2d");
    ctx.imageSmoothingEnabled = false;

    this.vueCanvas = ctx;

    this.vueCanvas.fillStyle = "#d4d6d4";
    this.vueCanvas.fillRect(0, 0, this.canvas.width, this.canvas.height);

    this.initZombieImages();
  },
  props: {
    waves: Array,
  },
  methods: {
    async playWave() {
      this.setStop(false);
      this.spawnedEnemies = 0;
      this.killedEnemies = 0;
      this.enemyStack = [];
      window.requestAnimationFrame(this.animationFrame);
      this.numberOfEnemies = this.getNumberOfEnemies();
      this.enemyStack = [];
      for (let i = 0; i < this.waves.length; i++) {
        for (let j = 0; j < this.waves[i][2]; j++) {
          while (
            this.spawnedEnemies - this.killedEnemies >=
            this.maxEnemiesOnScreen
          ) {
            if (this.isStopped) {
              this.resetWaves();
              return;
            }
            await new Promise((r) => setTimeout(r, 5));
          }
          this.addEnemyToStack(
            this.waves[i][1],
            this.angleHashMap.get(this.waves[i][0])[0],
            this.angleHashMap.get(this.waves[i][0])[1],
            this.waves[i][0]
          );
          this.spawnedEnemies += 1;
        }
      }
    },
    resetWaves() {
      // clear canvas
      this.isPaused = true;
      this.vueCanvas.clearRect(0, 0, this.canvas.width, this.canvas.height);
      this.vueCanvas.fillStyle = "#d4d6d4";
      this.vueCanvas.fillRect(0, 0, this.canvas.width, this.canvas.height);

      this.enemyStack = [];
      this.spawnedEnemies = 0;
      this.killedEnemies = 0;
    },
    setStop(c) {
      this.isStopped = c;
      this.resetWaves();
    },
    initZombieImages() {
      this.imageZ1 = [new Image(), 117, 188];
      this.imageZ1[0].src = require("../assets/z1.png");
      this.zombieHashMap.set("z1", {
        image: this.imageZ1,
        minVelocity: 40,
        maxVelocity: 70,
      });

      this.imageZ2 = [new Image(), 160, 145];
      this.imageZ2[0].src = require("../assets/z2.png");
      this.zombieHashMap.set("z2", {
        image: this.imageZ2,
        minVelocity: 50,
        maxVelocity: 70,
      });

      this.imageZ3 = [new Image(), 195, 255];
      this.imageZ3[0].src = require("../assets/z3.png");
      this.zombieHashMap.set("z3", {
        image: this.imageZ3,
        minVelocity: 50,
        maxVelocity: 70,
      });

      this.imageZ4 = [new Image(), 260, 294];
      this.imageZ4[0].src = require("../assets/z4.png");
      this.zombieHashMap.set("z4", {
        image: this.imageZ4,
        minVelocity: 50,
        maxVelocity: 70,
      });

      this.imageZ5 = [new Image(), 190, 233];
      this.imageZ5[0].src = require("../assets/z5.png");
      this.zombieHashMap.set("z5", {
        image: this.imageZ5,
        minVelocity: 50,
        maxVelocity: 70,
      });

      this.imageZ6 = [new Image(), 181, 195];
      this.imageZ6[0].src = require("../assets/z6.png");
      this.zombieHashMap.set("z6", {
        image: this.imageZ6,
        minVelocity: 50,
        maxVelocity: 70,
      });

      this.imageZ7 = [new Image(), 135, 142];
      this.imageZ7[0].src = require("../assets/z7.png");
      this.zombieHashMap.set("z7", {
        image: this.imageZ7,
        minVelocity: 50,
        maxVelocity: 70,
      });

      this.imageZ8 = [new Image(), 63, 72];
      this.imageZ8[0].src = require("../assets/z8.png");
      this.zombieHashMap.set("z8", {
        image: this.imageZ8,
        minVelocity: 100,
        maxVelocity: 120,
      });

      this.imageB1 = [new Image(), 320, 332];
      this.imageB1[0].src = require("../assets/b1.png");
      this.zombieHashMap.set("b1", {
        image: this.imageB1,
        minVelocity: 20,
        maxVelocity: 30,
      });

      this.townCenter = [new Image(), 422, 429];
      this.townCenter[0].src = require("../assets/town.png");
    },
    drawZombie(zombie) {
      this.vueCanvas.drawImage(
        zombie.enemy[0],
        zombie.x,
        zombie.y,
        zombie.enemy[1] * 0.5,
        zombie.enemy[2] * 0.5
      );
    },
    drawTown() {
      this.vueCanvas.drawImage(
        this.townCenter[0],
        789,
        1371,
        this.townCenter[1] * 1.2,
        this.townCenter[2] * 1.2
      );
    },
    startDrawing: function (elapsed) {
      this.enemyStack.forEach((z) => {
        this.drawZombie(z);
        this.moveZombie(z, elapsed);
      });
    },
    moveZombie(zombie, elapsed) {
      if (this.despawnEnemyCheck(zombie)) {
        let index = this.enemyStack.indexOf(zombie);
        this.enemyStack.splice(index, 1);
        this.killedEnemies += 1;
        return;
      }
      let data = this.distanceAndAngleBetweenTwoPoints(
        zombie.x,
        zombie.y,
        this.canvasCenter.x,
        this.canvasCenter.y
      );
      let vtc = this.vectorToCenter(
        zombie.velocity * this.playSpeed,
        data.angle
      );

      let elapsedSeconds = elapsed / 1000;

      zombie.x += vtc[0] * elapsedSeconds;
      zombie.y += vtc[1] * elapsedSeconds;
    },
    despawnEnemyCheck(zombie) {
      let distPoints =
        (this.canvasCenter.x - zombie.x) * (this.canvasCenter.x - zombie.x) +
        (this.canvasCenter.y - zombie.y) * (this.canvasCenter.y - zombie.y);
      let r = 80000;
      if (distPoints < r) {
        return true;
      }
      return false;
    },
    distanceAndAngleBetweenTwoPoints(x1, y1, x2, y2) {
      let x = x2 - x1,
        y = y2 - y1;

      return {
        distance: Math.sqrt(x * x + y * y),
        angle: (Math.atan2(y, x) * 180) / Math.PI,
      };
    },
    vectorToCenter(velocity, angle) {
      let angleRadians = (angle * Math.PI) / 180;
      let magnitudeX = velocity * Math.cos(angleRadians);
      let magnitudeY = velocity * Math.sin(angleRadians);
      return [magnitudeX, magnitudeY];
    },
    addEnemyToStack(enemy, x, y, angle) {
      let e = {
        enemy: this.zombieHashMap.get(enemy).image,
        x: x * 0.95 + Math.floor(Math.random() * (220 - -220 + 1)) - 220,
        y: y * 0.95 + Math.floor(Math.random() * (220 - -220 + 1)) + -220,
        angle: angle,
        velocity:
          Math.floor(
            Math.random() *
              (this.zombieHashMap.get(enemy).maxVelocity -
                this.zombieHashMap.get(enemy).minVelocity +
                1)
          ) + this.zombieHashMap.get(enemy).minVelocity,
      };
      this.enemyStack.push(e);
    },
    getNumberOfEnemies() {
      let zombieCount = 0;
      for (let i = 0; i < this.waves.length; i++) {
        zombieCount += this.waves[i][2];
      }
      return zombieCount;
    },
    animationFrame(milliseconds) {
      let elapsed = milliseconds - this.lastStep;
      this.lastStep = milliseconds;

      this.render(elapsed);
      window.requestAnimationFrame(this.animationFrame);
    },
    render(elapsed) {
      // clear canvas
      this.vueCanvas.clearRect(0, 0, this.canvas.width, this.canvas.height);
      this.vueCanvas.fillStyle = "#d4d6d4";
      this.vueCanvas.fillRect(0, 0, this.canvas.width, this.canvas.height);
      this.drawTown();
      this.startDrawing(elapsed);
    },
  },
};
</script>

<style>
canvas {
  width: 500px;
  height: 800px;
  padding: 1rem;
  border-radius: 2rem;
}

.player-container {
  display: flex;
  width: 100%;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  padding: 2em;
  background-image: linear-gradient(#b0c2b0, #869b86);
  padding: 1rem;
  margin: 1rem;
  border-radius: 15px;
}

.action-container {
  display: flex;
}

.stats {
  display:flex;
  gap: 1rem;
  width: 100%;
  padding: 0 2rem 0 2rem;
}

.stat-detail {
  padding: 0.5rem;
  background: #d4d6d4;
  border-radius: 15px;
  width: 100%;
}
</style>
