<template>
  <div :class="{ 'dark': isDarkMode }" class="flex flex-col items-center justify-center min-h-screen bg-gray-100 dark:bg-gray-900">
    <div v-if="countdown > 0" class="text-3xl font-bold text-gray-900 dark:text-gray-100">{{ countdown }}</div>
    <div v-else-if="resetCountdown > 0" class="text-3xl font-bold text-gray-900 dark:text-gray-100">{{ resetCountdown }}</div>
    <div v-else class="relative w-full h-full">
      <div
        v-for="(target, index) in targets"
        :key="index"
        class="target rounded-full absolute transition-all duration-500"
        :class="{
          'bg-red-500': !isDarkMode && !target.isOld,
          'bg-red-700': !isDarkMode && target.isOld,
          'bg-yellow-500': isDarkMode && !target.isOld,
          'bg-yellow-700': isDarkMode && target.isOld
        }"
        :style="{ top: target.y + 'px', left: target.x + 'px', width: targetSize + 'px', height: targetSize + 'px' }"
        @click="hitTarget(index)"
      ></div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Game',
  data() {
    return {
      countdown: 3,
      resetCountdown: 0,
      targets: [],
      targetCount: parseInt(this.$route.query.targetCount),
      minInterval: parseInt(this.$route.query.minInterval),
      maxInterval: parseInt(this.$route.query.maxInterval),
      resetInterval: parseInt(this.$route.query.resetInterval),
      targetSize: parseInt(this.$route.query.targetSize),
      isDarkMode: localStorage.getItem('isDarkMode') === 'true',
    };
  },
  mounted() {
    this.startCountdown();
  },
  methods: {
    startCountdown() {
      const countdownInterval = setInterval(() => {
        if (this.countdown > 0) {
          this.countdown--;
        } else {
          clearInterval(countdownInterval);
          this.spawnTargets();
        }
      }, 1000);
    },
    startResetCountdown() {
      this.resetCountdown = this.resetInterval / 1000;
      const resetCountdownInterval = setInterval(() => {
        if (this.resetCountdown > 0) {
          this.resetCountdown--;
        } else {
          clearInterval(resetCountdownInterval);
          setTimeout(this.spawnTargets, 500); // Dodaj krótkie opóźnienie przed rozpoczęciem generowania nowych punktów
        }
      }, 1000);
    },
    spawnTargets() {
      this.targets = [];
      let i = 0;
      const spawnInterval = setInterval(() => {
        if (i < this.targetCount) {
          const { x, y } = this.findBestPosition(this.targetSize);
          this.targets = this.targets.map(target => ({ ...target, isOld: true }));
          this.targets.push({ x, y, isOld: false });
          i++;
        } else {
          clearInterval(spawnInterval);
          this.targets = []
          this.startResetCountdown();
        }
      }, this.getRandomInterval());
    },
    findBestPosition(targetSize) {
      const gridSize = 10;
      const grid = Array.from({ length: gridSize }, () => Array(gridSize).fill(0));
      const width = window.innerWidth;
      const height = window.innerHeight;

      this.targets.forEach(target => {
        const gridX = Math.floor((target.x / width) * gridSize);
        const gridY = Math.floor((target.y / height) * gridSize);
        grid[gridY][gridX]++;
      });

      let maxEmptySpace = 0;
      let bestPosition = { x: width / 2, y: height / 2 };

      for (let y = 0; y < gridSize; y++) {
        for (let x = 0; x < gridSize; x++) {
          if (grid[y][x] === 0) {
            const emptySpace = this.calculateEmptySpace(grid, x, y);
            const randomFactor = Math.random(); // Dodaj losowy czynnik
            if (emptySpace * randomFactor > maxEmptySpace) {
              maxEmptySpace = emptySpace * randomFactor;
              bestPosition = {
                x: (x + 0.5) * (width / gridSize),
                y: (y + 0.5) * (height / gridSize),
              };
            }
          }
        }
      }

      return bestPosition;
    },
    calculateEmptySpace(grid, x, y) {
      const directions = [
        [0, 1], [1, 0], [0, -1], [-1, 0],
        [1, 1], [1, -1], [-1, 1], [-1, -1],
      ];
      let emptySpace = 0;

      directions.forEach(([dx, dy]) => {
        let nx = x + dx;
        let ny = y + dy;
        while (nx >= 0 && nx < grid.length && ny >= 0 && ny < grid[0].length && grid[ny][nx] === 0) {
          emptySpace++;
          nx += dx;
          ny += dy;
        }
      });

      return emptySpace;
    },
    getRandomInterval() {
      return Math.floor(Math.random() * (this.maxInterval - this.minInterval + 1)) + this.minInterval;
    },
    hitTarget(index) {
      this.targets.splice(index, 1);
    },
  },
};
</script>

<style scoped>
.target {
  position: absolute;
  border-radius: 50%;
}
</style>
