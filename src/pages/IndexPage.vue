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
        @click="download"
      />
    </div>
  </q-page>
</template>

<script setup>
function download() {
  const link = document.createElement('a')
  link.href =
    'https://github.com/stijn-rombouts/topdag-project-website/releases/download/0.1/test10.apk'
  link.download = 'test10.apk'
  link.click()
}
</script>
