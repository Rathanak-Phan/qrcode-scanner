<template>
  <div>
    <h2 class="text-xl font-bold mb-4">QR Code Scanner</h2>

    <div id="qr-reader" class="w-full mx-auto mb-4"></div>

    <p class="text-gray-700 mb-2"><strong>Result:</strong></p>
    <div class="flex items-center gap-4">
      <span v-if="!isURL(result)">{{ result }}</span>

      <a
        v-else
        :href="result"
        target="_blank"
        rel="noopener noreferrer"
        class="text-blue-500 underline"
      >
        {{ result }}
      </a>

      <button
        v-if="result !== 'None'"
        @click="copyResult"
        class="bg-gray-300 px-2 py-1 rounded hover:bg-gray-400"
      >
        Copy
      </button>
    </div>

    <input
      type="file"
      accept="image/*"
      class="mt-3 p-2 border rounded"
      @change="scanImage"
    />
  </div>
</template>

<script>
import { Html5Qrcode } from "html5-qrcode";

export default {
  data() {
    return {
      qrScanner: null,
      result: "None",
    };
  },

  mounted() {
    this.startCamera();
  },

  beforeUnmount() {
    if (this.qrScanner) {
      this.qrScanner.stop();
    }
  },

  methods: {
    async startCamera() {
      this.qrScanner = new Html5Qrcode("qr-reader");

      try {
        const cameras = await Html5Qrcode.getCameras();
        if (cameras.length) {
          this.qrScanner.start(
            cameras[0].id,
            { fps: 10, qrbox: 250 },
            (decoded) => {
              this.result = decoded;
            }
          );
        }
      } catch (err) {
        console.error("Camera error:", err);
      }
    },

    async scanImage(event) {
      const file = event.target.files[0];
      if (!file) return;

      const scanner = new Html5Qrcode("qr-reader");
      try {
        const decoded = await scanner.scanFile(file, true);
        this.result = decoded;
      } catch (err) {
        this.result = "Error reading QR image!";
      }
    },

    isURL(text) {
      try {
        return text.startsWith("http://") || text.startsWith("https://");
      } catch {
        return false;
      }
    },

    copyResult() {
      navigator.clipboard.writeText(this.result).then(
        () => alert("Copied to clipboard!"),
        (err) => alert("Failed to copy: " + err)
      );
    },
  },
};
</script>
