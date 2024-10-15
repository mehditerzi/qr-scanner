<template>
  <div class="wrapper">
    <video ref="videoRef"></video>
    <button @click="startScanner">Tara</button>
  </div>
</template>

<script setup>
import { ref, onBeforeUnmount } from 'vue';
import QrScanner from 'qr-scanner';

const videoRef = ref(null);
let qrScanner = null;
let scanning = false;

function onScanSuccess(result) {
  if (!scanning) return; // Prevent multiple alerts
  scanning = false; // Pause scanning

  // Show the result in a pop-up
  alert(`Misafir İsmi: ${result}`);

  // Resume scanning after the pop-up is closed
  scanning = true;
}

function onScanError(error) {
  console.error(error);
}

function startScanner() {
  if (process.client) {
    const videoElement = videoRef.value;

    // Set the path to the worker script
    QrScanner.WORKER_PATH = '/qr-scanner-worker.min.js';

    qrScanner = new QrScanner(
        videoElement,
        (result) => onScanSuccess(result),
        (error) => onScanError(error)
    );

    qrScanner.start().then(() => {
      scanning = true; // Start scanning
    }).catch((error) => {
      console.error(error);
      alert(`Kamera başlatılamadı: ${error}`);
    });
  }
}

onBeforeUnmount(() => {
  if (qrScanner) {
    qrScanner.destroy();
    qrScanner = null;
  }
});
</script>

<style scoped>
.wrapper{
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  gap: 100px;
  width: 100%;
  height: 100%;
}
video {
  border: 1px solid #ccc;
  border-radius: 4px;
  width: 100%;
  height: auto;
}
button{
  width: 5em;
  height: 2em;
  font-size: 4em;
  border-radius: 1em;
}
</style>