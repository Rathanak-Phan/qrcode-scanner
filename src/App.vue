<template>
  <div class="min-h-screen bg-gray-100 p-6">
    <div class="max-w-2xl mx-auto bg-white shadow-xl p-6 rounded-xl">
      <!-- Logo Section -->
      <div class="flex justify-center mb-6">
        <img
          src="../public/logo.png"
          alt="QR Logo"
          class="w-32 h-32 object-contain rounded-full border-4 border-blue-200 shadow-md"
        />
      </div>
      <h1 class="text-3xl font-bold text-center mb-6">
        QR Code Tool (Vue + Tailwind)
      </h1>

      <div class="flex mb-6 border-b">
        <button
          class="flex-1 py-2 text-center"
          :class="[
            tab === 'scan'
              ? 'border-b-2 border-blue-500 font-bold'
              : 'text-gray-500',
          ]"
          @click="tab = 'scan'"
        >
          Generate QR
        </button>

        <button
          class="flex-1 py-2 text-center"
          :class="[
            tab === 'generate'
              ? 'border-b-2 border-blue-500 font-bold'
              : 'text-gray-500',
          ]"
          @click="tab = 'generate'"
        >
          Scan QR
        </button>
      </div>

      <QrScanner v-if="tab === 'scan'" />
      <QrGenerator v-if="tab === 'generate'" />
    </div>
  </div>
</template>

<script>
import QrScanner from "./components/QrGenerator.vue";
import QrGenerator from "./components/QrScanner.vue";

export default {
  components: { QrScanner, QrGenerator },
  data() {
    return { tab: "scan" };
  },
};
</script>
