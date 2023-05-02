<template>
  <div>
    <button @click="startRecording" :disabled="recording || playing">
      Iniciar Grabación
    </button>
    <button @click="stopRecording" :disabled="!recording || playing">
      Detener Grabación
    </button>
    <button @click="playRecording" :disabled="!recordingFinished || playing">
      Reproducir grabación
    </button>
  </div>
</template>

<script setup>
import { ref } from "vue";

let mediaRecorder = null;
let chunks = [];
const recording = ref(false);
const recordingFinished = ref(false);
const playing = ref(false);
const audioUrl = ref("");

const startRecording = () => {
  mediaRecorder.start();
  recording.value = true;
  recordingFinished.value = false;
};

const stopRecording = () => {
  mediaRecorder.stop();
};

const playRecording = () => {
  const audio = new Audio(audioUrl.value);
  audio.play();
  playing.value = true;
  audio.onended = () => {
    playing.value = false;
  };
};

navigator.mediaDevices
  .getUserMedia({ audio: true })
  .then((stream) => {
    mediaRecorder = new MediaRecorder(stream);
    mediaRecorder.ondataavailable = (e) => {
      chunks.push(e.data);
    };
    mediaRecorder.onstop = () => {
      recordingFinished.value = true;
      //chunks = [];
      recording.value = false;
      const blob = new Blob(chunks, { type: "audio/mp3" });
      console.log("Audio file size:", blob.size);
      audioUrl.value = URL.createObjectURL(blob);
    };
  })
  .catch((err) => {
    console.log(err);
  });
</script>

<style scoped></style>
