<template>
  <div
    :class="currentState == 0 ? 'bg-black' : 'bg-blue-950'"
    class="flex flex-col gap-4 items-center justify-center min-h-screen w-full text-white transition ease-in-out duration-1000"
  >
    <h1 class="text-9xl">{{ formatMinutesToTime(currentTime) }}</h1>
    <span>{{ currentState == 0 ? "focus!" : "take a break..." }}</span>
    <button class="text-white/30" @click="toggleTimer">
      {{ timerActive ? "PAUSE" : "START" }}
    </button>
  </div>
</template>
<script setup lang="ts">
import { onMounted, ref } from "vue";

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

onMounted(() => {
  currentTime.value = times.value[currentState.value];
});
</script>
