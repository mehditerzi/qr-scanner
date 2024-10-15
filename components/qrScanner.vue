<template>
  <div>
    <button @click="startScanner">Start Scanner</button>
    <video ref="videoRef" style="width: 100%; max-width: 500px;"></video>
  </div>
</template>

<script setup>
import { ref, onBeforeUnmount } from 'vue';
import QrScanner from 'qr-scanner';

const videoRef = ref(null);
let qrScanner = null;

function onScanSuccess(result) {
  // Show the result in a pop-up
  alert(`QR Code content: ${result}`);
  // Optionally, stop the scanner after a successful scan
  qrScanner.stop();
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

    qrScanner.start().catch((error) => {
      console.error(error);
      alert(`Could not start camera: ${error}`);
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
video {
  border: 1px solid #ccc;
  border-radius: 4px;
}
</style>