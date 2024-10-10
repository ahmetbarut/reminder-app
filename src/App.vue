<template>
  <div class="min-h-screen flex flex-col items-center justify-center bg-gray-100">
    <div class="bg-white p-8 rounded-lg shadow-lg">
      <h1 class="text-2xl font-bold mb-4">Reminder App</h1>
      <label class="block mb-2">
        Reminder Interval (minutes):
        <input v-model="interval" type="number" class="border p-2 w-full">
      </label>
      <button @click="startReminder" class="bg-blue-500 text-white px-4 py-2 rounded">
        Start Reminder
      </button>
      <div v-if="timeLeft !== null" class="mt-4 text-lg">
        Time left: {{ formatTime(timeLeft) }}
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const interval = ref(5); // Default interval in minutes
const audio = ref(new Audio('/muzik.mp3'));
const timeLeft = ref(null);
let countdownInterval = null;

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
    new Notification("Reminder!", {
      body: `Your reminder has arrived!`,
    });
  } else if (Notification.permission !== "denied") {
    Notification.requestPermission().then((permission) => {
      if (permission === "granted") {
        new Notification("Reminder!", {
          body: `Your reminder has arrived!`,
        });
      }
    });
  }
};

const playAudio = () => {
  audio.value.currentTime = 0; // Reset audio to the beginning
  audio.value.play();
};

const formatTime = (seconds) => {
  const mins = Math.floor(seconds / 60);
  const secs = seconds % 60;
  return `${mins}m ${secs}s`;
};
</script>
