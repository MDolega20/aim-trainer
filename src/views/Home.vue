<template>
  <div :class="{ 'dark': isDarkMode }" class="flex flex-col items-center justify-center min-h-screen bg-gray-100 dark:bg-gray-900">
    <h1 class="text-4xl font-bold mb-8 text-gray-900 dark:text-gray-100">Witaj w Aim Trainer</h1>
    <div class="mb-8">
      <button @click="setPreset('easy')" class="preset-button bg-green-500 text-white py-4 px-8 rounded-md hover:bg-green-600 focus:outline-none focus:ring-4 focus:ring-offset-2 focus:ring-green-500 mr-4">Łatwy</button>
      <button @click="setPreset('medium')" class="preset-button bg-yellow-500 text-white py-4 px-8 rounded-md hover:bg-yellow-600 focus:outline-none focus:ring-4 focus:ring-offset-2 focus:ring-yellow-500 mr-4">Średni</button>
      <button @click="setPreset('hard')" class="preset-button bg-red-500 text-white py-4 px-8 rounded-md hover:bg-red-600 focus:outline-none focus:ring-4 focus:ring-offset-2 focus:ring-red-500">Trudny</button>
    </div>
    <form @submit.prevent="startGame" class="bg-white dark:bg-gray-800 p-6 rounded-lg shadow-md w-full max-w-md">
      <div class="mb-4">
        <label for="targetCount" class="block text-sm font-medium text-gray-700 dark:text-gray-300">Liczba celów: {{ targetCount }}</label>
        <input type="range" v-model="targetCount" min="1" max="10" required class="mt-1 block w-full" />
      </div>

      <div class="mb-4">
        <label for="minInterval" class="block text-sm font-medium text-gray-700 dark:text-gray-300">Minimalny interwał wyświetlania (ms): {{ minInterval }}</label>
        <input type="range" v-model="minInterval" min="100" max="2000" step="100" required class="mt-1 block w-full" />
      </div>

      <div class="mb-4">
        <label for="maxInterval" class="block text-sm font-medium text-gray-700 dark:text-gray-300">Maksymalny interwał wyświetlania (ms): {{ maxInterval }}</label>
        <input type="range" v-model="maxInterval" :min="minInterval" max="3000" step="100" required class="mt-1 block w-full" />
      </div>

      <div class="mb-4">
        <label for="resetInterval" class="block text-sm font-medium text-gray-700 dark:text-gray-300">Interwał resetowania (ms): {{ resetInterval }}</label>
        <input type="range" v-model="resetInterval" min="1000" max="10000" step="500" required class="mt-1 block w-full" />
      </div>

      <div class="mb-4">
        <label for="targetSize" class="block text-sm font-medium text-gray-700 dark:text-gray-300">Rozmiar celu (px): {{ targetSize }}</label>
        <input type="range" v-model="targetSize" min="10" max="100" step="5" required class="mt-1 block w-full" />
      </div>

      <button type="submit" class="w-full bg-indigo-600 text-white py-4 px-8 rounded-md hover:bg-indigo-700 focus:outline-none focus:ring-4 focus:ring-offset-2 focus:ring-indigo-500">Rozpocznij grę</button>
    </form>
  </div>
</template>

<script>
export default {
  name: 'Home',
  data() {
    return {
      targetCount: 5,
      minInterval: 1000,
      maxInterval: 2000,
      resetInterval: 5000,
      targetSize: 50,
      isDarkMode: false,
    };
  },
  watch: {
    isDarkMode(newValue) {
      localStorage.setItem('isDarkMode', newValue);
      if (newValue) {
        document.documentElement.classList.add('dark');
      } else {
        document.documentElement.classList.remove('dark');
      }
    },
  },
  mounted() {
    this.isDarkMode = localStorage.getItem('isDarkMode') === 'true';
  },
  methods: {
    startGame() {
      this.$router.push({
        name: 'Game',
        query: {
          targetCount: this.targetCount,
          minInterval: this.minInterval,
          maxInterval: this.maxInterval,
          resetInterval: this.resetInterval,
          targetSize: this.targetSize,
        },
      });
    },
    setPreset(preset) {
      if (preset === 'easy') {
        this.targetCount = 3;
        this.minInterval = 1000;
        this.maxInterval = 2000;
        this.resetInterval = 5000;
        this.targetSize = 70;
      } else if (preset === 'medium') {
        this.targetCount = 5;
        this.minInterval = 700;
        this.maxInterval = 1500;
        this.resetInterval = 4000;
        this.targetSize = 50;
      } else if (preset === 'hard') {
        this.targetCount = 7;
        this.minInterval = 500;
        this.maxInterval = 1000;
        this.resetInterval = 3000;
        this.targetSize = 30;
      }
    },
  },
};
</script>

<style scoped>
.preset-button {
  font-size: 1.5rem; /* Zwiększ rozmiar czcionki */
  padding: 1rem 2rem; /* Zwiększ padding */
}
</style>
