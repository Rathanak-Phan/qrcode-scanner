<template>
  <div class="p-4 bg-gray-50 rounded-lg shadow-md max-w-md mx-auto">
    <h2 class="text-2xl font-bold mb-4 text-center text-blue-600">QR Code Generator</h2>

    <!-- Text Input -->
    <input
      v-model="text"
      type="text"
      placeholder="Enter text or URL..."
      class="border p-2 w-full rounded mb-4 focus:outline-none focus:ring-2 focus:ring-blue-400"
    />

    <!-- Logo Upload -->
    <label class="block mb-4">
      <span class="text-gray-700 mb-1">Upload Logo (optional)</span>
      <input type="file" accept="image/*" @change="handleLogoUpload" class="block w-full text-sm text-gray-500 underline"/>
    </label>

    <!-- Generate Button -->
    <button
      @click="generateQR"
      class="w-full bg-blue-500 text-white px-4 py-2 rounded mb-4 hover:bg-blue-600 transition-colors"
    >
      Generate QR
    </button>

    <!-- QR Canvas -->
    <div class="flex justify-center mb-4">
      <canvas ref="qrCanvas" class="border rounded-md"></canvas>
    </div>

    <!-- Download Button -->
    <button
      v-if="qrGenerated"
      @click="downloadQR"
      class="w-full bg-green-500 text-white px-4 py-2 rounded hover:bg-green-600 transition-colors"
    >
      Download QR
    </button>
  </div>
</template>

<script>
import QRCode from "qrcode";

export default {
  data() {
    return {
      text: "",
      qrGenerated: false,
      logoFile: null, // Store uploaded logo
    };
  },
  methods: {
    // Handle user logo upload
    handleLogoUpload(event) {
      const file = event.target.files[0];
      if (!file) return;
      this.logoFile = file;
    },

    async generateQR() {
      if (!this.text.trim()) return;

      const canvas = this.$refs.qrCanvas;

      // 1. Generate QR code
      await QRCode.toCanvas(canvas, this.text, {
        width: 300,
        margin: 2,
        color: { dark: "#000000", light: "#ffffff" },
      });

      // 2. Add logo if uploaded
      if (this.logoFile) {
        const reader = new FileReader();
        reader.onload = (e) => {
          const logo = new Image();
          logo.src = e.target.result;
          logo.onload = () => {
            const ctx = canvas.getContext("2d");

            // Logo size: 20% of QR width (safe for scanning)
            const logoSize = canvas.width * 0.2;
            const x = (canvas.width - logoSize) / 2;
            const y = (canvas.height - logoSize) / 2;

            // Optional: Add white background behind logo for better scan
            const padding = 6; // space between logo and QR dots
            ctx.fillStyle = "#ffffff";
            ctx.fillRect(x - padding, y - padding, logoSize + padding * 2, logoSize + padding * 2);

            // Draw logo
            ctx.drawImage(logo, x, y, logoSize, logoSize);
          };
        };
        reader.readAsDataURL(this.logoFile);
      }

      this.qrGenerated = true;
    },

    downloadQR() {
      const canvas = this.$refs.qrCanvas;
      const link = document.createElement("a");
      link.download = "qr-code.png";
      link.href = canvas.toDataURL("image/png");
      link.click();
    },
  },
};
</script>
