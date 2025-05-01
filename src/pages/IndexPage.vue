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
import axios from 'axios'

async function download() {
  try {
    const response = await axios.get(
      'https://api.github.com/repos/stijn-rombouts/topdag-project-website/releases/latest',
    )
    const asset = response.data.assets.find((a) => a.name.endsWith('.apk'))
    if (asset) {
      const link = document.createElement('a')
      link.href = asset.browser_download_url
      link.download = asset.name
      link.click()
    } else {
      console.error('No APK file found in the latest release.')
    }
  } catch (error) {
    console.error('Failed to fetch the latest release:', error)
  }
}
</script>
