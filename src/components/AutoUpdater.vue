<template>
  <div></div>
</template>

<script type="text/javascript">
import { ipcRenderer } from 'electron'
import Noty from 'noty'

import AppEvent from '../common/AppEvent'

export default {
  data() {
    return {
      manualNotification: new Noty({
        text: "A new version is available. Download from our website now.",
        layout: 'bottomRight',
        timeout: false,
        closeWith: 'button',
        buttons: [ 
          Noty.button('Not now', 'btn btn-flat', () => {
            this.manualNotification.close();
          }),
          Noty.button('Download', 'btn btn-primary', this.linkToDownload)
        ]
      }),
      downloadNotification: new Noty({
        text: 'A new version is availble. Download now?',
        layout: 'bottomRight',
        timeout: false,
        closeWith: 'button',
        buttons: [
          Noty.button('Not now', 'btn btn-flat', () => {
              this.downloadNotification.close();
          }),
          Noty.button('Download', 'btn btn-primary', this.triggerDownload)
        ]
      }),
      installNotification: new Noty({
        text: "Update downloaded. Restart Beekeeper Studio to install",
        layout: 'bottomRight',
        timeout: false,
        closeWith: 'button',
        buttons: [
          Noty.button('Later', 'btn btn-flat', () => {
            this.installNotification.close()
          }),
          Noty.button('Restart Now', 'btn btn-primary', this.triggerInstall)
        ]
      })

    }
  },
  mounted() {
    ipcRenderer.on('update-available', this.notifyUpdate)
    ipcRenderer.on('manual-update', this.notifyManual)
    ipcRenderer.on('update-downloaded', this.notifyDownloaded)
    ipcRenderer.send('updater-ready')
  },
  methods: {
    triggerDownload() {
      ipcRenderer.send('download-update')
      this.downloadNotification.close()
      this.$noty.info("Hold tight! Downloading update...")
    },
    notifyManual() {
      this.manualNotification.show()
    },
    linkToDownload() {
      ipcRenderer.send(AppEvent.openExternally, ["https://beekeeperstudio.io/get"])
    },
    triggerInstall() {
      ipcRenderer.send('install-update')
    },
    notifyUpdate() {
      this.downloadNotification.show();
    },
    notifyDownloaded() {
      this.installNotification.show();
    }
  }
}

</script>
