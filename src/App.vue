<template>
  <div
    class="min-h-screen flex flex-col items-center justify-center bg-gradient-to-r from-red-800 via-pink-900 to-black"
  >
    <div
      class="bg-white p-4 rounded-lg shadow-lg flex flex-col items-center justify-center"
    >
      <div>
        <div
          v-if="timeLeft !== null"
          class="mt-8 text-3xl font-bold bg-red-500 text-white h-32 w-32 text-center justify-center items-center flex rounded-full p-1"
        >
          <span>{{ formatTime(timeLeft) }}</span>
        </div>
      </div>
      <div>
        <h1 class="text-2xl font-bold mb-4">Hatırlatıcım</h1>
        <label class="block mb-2">
          Ne Zaman Hatırlatsın (DK)
          <input
            v-model="interval"
            type="number"
            class="border p-2 w-full"
            placeholder="Dakika"
          />
        </label>
        <button
          @click="startReminder"
          v-if="isMusicPlayed === false"
          class="text-white bg-gradient-to-r from-red-800 to-pink-900 px-4 py-2 rounded w-full"
        >
          Hatırlatıcıyı Başlat
        </button>
        <button
          v-else
          @click="resetReminder"
          class="text-white bg-gradient-to-r from-blue-800 to-purple-900 px-4 py-2 rounded w-full"
        >
          Hatırlatıcıyı Durdur
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from "vue";

const interval = ref(5); // Default interval in minutes
const audio = ref(new Audio("/muzik.mp3"));
const timeLeft = ref(null);
let countdownInterval = null;
const isMusicPlayed = ref(false);

const startReminder = () => {
  if (!audio.value) {
    alert("Please upload a music file.");
    return;
  }

  timeLeft.value = interval.value * 60; // Convert minutes to seconds
  countdownInterval = setInterval(() => {
    if (timeLeft.value > 0) {
      timeLeft.value--;
    } else {
      scheduleReminder();
      timeLeft.value = interval.value * 60; // Reset timer
    }
  }, 1000); // Update every second
};

const scheduleReminder = () => {
  showNotification();
  playAudio();
};

const showNotification = () => {
  if (Notification.permission === "granted") {
    new Notification("Hatırlatıcı", {
      body: `Hatırlatmanız geldi!`,
    });
  } else if (Notification.permission !== "denied") {
    Notification.requestPermission().then((permission) => {
      if (permission === "granted") {
        new Notification("Hatırlatıcı", {
          body: `Hatırlatmanız geldi!`,
        });
      }
    });
  }
};

const playAudio = () => {
  audio.value.currentTime = 0; // Reset audio to the beginning
  audio.value.play();
  isMusicPlayed.value = true;
};

const stopAudio = () => {
  audio.value.load();
  isMusicPlayed.value = false;
};

const resetReminder = () => {
  timeLeft.value = interval.value * 60;
  stopAudio();
}

const formatTime = (seconds) => {
  const mins = Math.floor(seconds / 60);
  const secs = seconds % 60;
  return `${mins}d ${secs}s`;
};
</script>
