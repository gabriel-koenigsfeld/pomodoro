<template>
  <div
    :class="currentState == 0 ? 'bg-black' : 'bg-blue-950'"
    class="flex flex-col gap-8 items-center justify-center min-h-screen w-full text-white transition ease-in-out duration-1000"
  >
    <h1 class="text-9xl">{{ formatMinutesToTime(currentTime) }}</h1>
    <div class="flex flex-row gap-4">
      <button
        aria-label="Pause and Play Timer"
        class="text-white bg-white/10 hover:bg-white/20 active:bg-white/5 transition ease-in-out duration-75 py-1 px-4 rounded-md"
        @click="toggleTimer"
      >
        <svg
          v-if="timerActive"
          xmlns="http://www.w3.org/2000/svg"
          viewBox="0 0 24 24"
          fill="currentColor"
          class="w-6 h-6"
        >
          <path
            fill-rule="evenodd"
            d="M6.75 5.25a.75.75 0 0 1 .75-.75H9a.75.75 0 0 1 .75.75v13.5a.75.75 0 0 1-.75.75H7.5a.75.75 0 0 1-.75-.75V5.25Zm7.5 0A.75.75 0 0 1 15 4.5h1.5a.75.75 0 0 1 .75.75v13.5a.75.75 0 0 1-.75.75H15a.75.75 0 0 1-.75-.75V5.25Z"
            clip-rule="evenodd"
          />
        </svg>
        <svg
          v-else
          xmlns="http://www.w3.org/2000/svg"
          viewBox="0 0 24 24"
          fill="currentColor"
          class="w-6 h-6"
        >
          <path
            fill-rule="evenodd"
            d="M4.5 5.653c0-1.427 1.529-2.33 2.779-1.643l11.54 6.347c1.295.712 1.295 2.573 0 3.286L7.28 19.99c-1.25.687-2.779-.217-2.779-1.643V5.653Z"
            clip-rule="evenodd"
          />
        </svg>
      </button>
      <button
        aria-label="Reset Timer"
        class="text-white bg-white/10 hover:bg-white/20 active:bg-white/5 transition ease-in-out duration-75 py-1 px-4 rounded-md"
        @click="resetTimer"
      >
        <svg
          class="w-6 h-6"
          fill="currentColor"
          viewBox="0 0 24 24"
          xmlns="http://www.w3.org/2000/svg"
        >
          <path
            d="M12 16c1.671 0 3-1.331 3-3s-1.329-3-3-3-3 1.331-3 3 1.329 3 3 3z"
          />
          <path
            d="M20.817 11.186a8.94 8.94 0 0 0-1.355-3.219 9.053 9.053 0 0 0-2.43-2.43 8.95 8.95 0 0 0-3.219-1.355 9.028 9.028 0 0 0-1.838-.18V2L8 5l3.975 3V6.002c.484-.002.968.044 1.435.14a6.961 6.961 0 0 1 2.502 1.053 7.005 7.005 0 0 1 1.892 1.892A6.967 6.967 0 0 1 19 13a7.032 7.032 0 0 1-.55 2.725 7.11 7.11 0 0 1-.644 1.188 7.2 7.2 0 0 1-.858 1.039 7.028 7.028 0 0 1-3.536 1.907 7.13 7.13 0 0 1-2.822 0 6.961 6.961 0 0 1-2.503-1.054 7.002 7.002 0 0 1-1.89-1.89A6.996 6.996 0 0 1 5 13H3a9.02 9.02 0 0 0 1.539 5.034 9.096 9.096 0 0 0 2.428 2.428A8.95 8.95 0 0 0 12 22a9.09 9.09 0 0 0 1.814-.183 9.014 9.014 0 0 0 3.218-1.355 8.886 8.886 0 0 0 1.331-1.099 9.228 9.228 0 0 0 1.1-1.332A8.952 8.952 0 0 0 21 13a9.09 9.09 0 0 0-.183-1.814z"
          />
        </svg>
      </button>
    </div>
  </div>
</template>
<script setup lang="ts">
import { onMounted, ref, watch } from "vue";

const times = ref<number[]>([1500, 300]);
const bigPause = ref<number>(900);

const currentTime = ref<number>(0);
const timerActive = ref<boolean>(false);

const currentState = ref<number>(0);

const bigPauseCounter = ref<number>(0);
const bigPauseOccurance = ref<number>(3);

let interval: number | undefined;

// FORMATTER
const formatMinutesToTime = (minutes: number) => {
  // Convert total minutes to hours and minutes
  var hours = Math.floor(minutes / 60);
  var remainingMinutes = minutes % 60;

  // Format hours and minutes with leading zeros if needed
  var formattedHours = hours < 10 ? "0" + hours : hours;
  var formattedMinutes =
    remainingMinutes < 10 ? "0" + remainingMinutes : remainingMinutes;

  // Construct the formatted time string
  var formattedTime = formattedHours + ":" + formattedMinutes;

  return formattedTime;
};

// TOGGLE BUTTON
const toggleTimer = () => {
  timerActive.value = !timerActive.value;

  if (!timerActive.value) {
    clearInterval(interval);
    return;
  }
  if (timerActive.value) {
    interval = setInterval(() => {
      decrementTime();
    }, 1000);
  }
};

// DECREMENT TIMER
const decrementTime = () => {
  currentTime.value -= 1;

  if (currentTime.value < 0) {
    switchTimer();
  }
};

const switchTimer = () => {
  currentState.value += 1;

  if (currentState.value == 1) {
    bigPauseCounter.value += 1;
  }

  if (currentState.value > times.value.length - 1) {
    currentState.value = 0;
  }

  if (bigPauseCounter.value >= bigPauseOccurance.value) {
    currentTime.value = bigPause.value;
    bigPauseCounter.value = 0;
    return;
  }
  currentTime.value = times.value[currentState.value];
};

const resetTimer = () => {
  toggleTimer();
  currentState.value = 0;
  bigPauseCounter.value = 0;
  currentTime.value = times.value[currentState.value];
};

onMounted(() => {
  currentTime.value = times.value[currentState.value];
});

watch(currentTime, (newTime) => {
  document.title = formatMinutesToTime(newTime);
});
</script>
<style>
body {
  font-family: "Gill Sans", "Gill Sans MT", Calibri, "Trebuchet MS", sans-serif;
}
</style>
