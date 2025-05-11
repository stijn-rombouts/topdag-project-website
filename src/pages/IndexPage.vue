<template>
  <q-page class="flex flex-center bg-dark">
    <div class="column items-center">
      <img
        alt="FutureLens Logo"
        src="~assets/FutureLensLogoFull.png"
        style="width: 200px; height: 200px"
      />
      <q-separator spaced inset vertical dark />
      <q-btn
        outline
        rounded
        icon="download"
        color="orange"
        class="text-weight-bolder"
        label="Download"
        size="xl"
        @click="downloadApk"
      />
    </div>
  </q-page>
</template>

<script setup>
function downloadApk() {
  const apkUrl = 'https://github.com/stijn-rombouts/topdag-project-website/releases/download/1.4/FutureLensV1.4.0.apk';
  const anchor = document.createElement('a');
  anchor.href = apkUrl;
  anchor.download = ''; // Optional: specify a filename here
  document.body.appendChild(anchor);
  anchor.click();
  document.body.removeChild(anchor);
}
</script>
