<script setup>
import { ref, onMounted } from "vue";
import * as faceapi from "face-api.js";
const videoRef = ref(null);
const canvasRef = ref(null);
const message = ref("");
onMounted(async () => {
  // Load the models
  const stream = await navigator.mediaDevices.getUserMedia({
    video: true,
    audio: false,
  });
  videoRef.value.srcObject = stream;

  await faceapi.nets.tinyFaceDetector.loadFromUri("/models");
  setInterval(async () => {
    const video = videoRef.value;

    // Detect faces
    const detections = await faceapi.detectAllFaces(
      video,
      new faceapi.TinyFaceDetectorOptions()
    );

    // Check for conditions
    if (detections.length === 0) {
      message.value = "No face detected";
    } else if (detections.length > 1) {
      message.value = "Multiple faces detected";
    }else{
      message.value = "Only one face detected";
    }
  }, 100); // Run detection every 100ms
});
</script>

<template>
  <div class="App">
    <p class="message">{{message}}</p>
    <video ref="videoRef" autoplay playsinline width="640" height="480"></video>
  </div>
</template>

<style scoped>
.App{
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background-color: #f0f0f0;
}
.message{
  position:absolute;
  top: 10px;
  width: 50vw;
  height: 5vh;
  left: 50%;
  transform: translateX(-50%);
  color: white;
  background-color: red;
  padding: 5px;
  border-radius: 5px;
}
</style>
