<template>
  <div id="dbr-instance">
    <div id="tab-switch">
      <span :class="['a-link', activeTab==='reader'?'text-strong':'']"><a href="#" @click="switchTab(0)">Reader</a></span>
      <span class="a-link">|</span>
      <span :class="['a-link', activeTab==='scanner'?'text-strong':'']"><a href="#" @click="switchTab(1)">Scanner</a></span>
    </div>
    <!-- File Decoding -->
    <div id="reader" :class="activeTab==='reader'?'':'display-none'">
      <button class="btn-primary" @click="handleFileOpenBtnClick">Open File</button>
      <input type="file" 
            id="file-selector" 
            style="display: none" 
            @change="handleFileSelectorChange"
            @click="handleFileSelectorClick"
      />
    </div>
    <!-- Live Scanning -->
    <div id="scanner" :class="activeTab==='scanner'?'':'display-none'">
      <button class="btn-secondary" @click="toggleVideo">Start</button>
      <div id="video-wrapper">
        <video class="dbrScanner-video"></video>
        <div id="video-info-board">
          <!-- <h2 style="padding: 0;margin: 0;">Video Information</h2> -->
        </div>
      </div>
    </div>
    <div id="result-wrapper">
      <ResultCard v-for="(res, idx) in results" :key="idx"
                  :format="res.format" :text="res.text"
      />
    </div>
  </div>
</template>

<script>
import dbr from 'dynamsoft-javascript-barcode'
import ResultCard from './ResultCard'

export default {
  name: 'dbr',
  props: ['license'],
  components: {
    ResultCard
  },
  data: () => ({
    activeTab: 'reader',
    reader: {
      obj: null
    },
    scanner: {
      obj: null,
      videoEnabled: false
    },
    frameBuffer: {
      imgData: Uint8Array.from([255]),
      width: 1,
      height: 1,
      channel: 4
    },
    results: []
  }),
  computed: {

  },
  watch: {
    
  },
  async mounted () {
    await this.init()
    this.bindScannerEvents()
    await this.bindVideoPreviewer()
  },
  methods: {
    init: async function () {
      let initProcess = async () => {
        const reader = dbr.BarcodeReader
        reader.engineResourcePath = '/dbr-resources'
        reader.productKeys = this.license
        this.reader.obj = await reader.createInstance()
        const scanner = dbr.BarcodeScanner
        this.scanner.obj = await scanner.createInstance()
        return true
      }
      await initProcess()
    },
    bindScannerEvents: function () {
      console.log(this.scanner.obj)
      this.scanner.obj.onFrameRead = results => { this.updateResultBuffer(results) }
    },
    bindVideoPreviewer: function () {
      const video = document.querySelector('video')
      return this.scanner.obj.setUIElement(video)
    },
    toggleVideo: async function() {
      this.scanner.videoEnabled = !this.scanner.videoEnabled
      if (this.scanner.videoEnabled)
        await this.scanner.obj.show()
      else
        await this.scanner.obj.stop()
      // const camera = navigator.mediaDevices.getUserMedia({ video: { width: 1280, height: 720 } })
      // const video = document.querySelector('video')
      // camera.then(stream => {
      //   video.srcObject = stream
      //   video.play()
      // })
    },
    switchTab: function (tabIdx) {
      switch (tabIdx) {
        default: 
        case 0: this.activeTab = 'reader'; break;
        case 1: this.activeTab = 'scanner'; break;
      }
    },
    handleFileSelectorChange: function (evt) {
      this.openFileSync(evt.target.files[0])
    },
    handleFileSelectorClick: function (evt) {
      evt.target.value = ''
    },
    /**
     * Open and decode the file
     */
    handleFileOpenBtnClick: function () {
      const selector = document.getElementById('file-selector')
      selector.click()
    },
    openFileSync: async function (file) {
      let reader = new FileReader()
      let imgBuffer = await new Promise((res, rej) => {
        reader.onload = response => {
          res(response.target.result)
        }
        reader.onerror = err => {
          rej(err)
        }

        reader.readAsArrayBuffer(file)
      })
        .catch(err => {
          console.error(err)
        })
      let results = await this.decode(file)
      console.log(results)
      this.updateResultBuffer(results)
    },
    decode: async function (data) {
      return this.reader.obj.decode(data)
    },
    updateResultBuffer: function (rawMsg) {
      window.console.log(rawMsg)
      this.results = rawMsg.map(res => ({format: res.barcodeFormatString, text: res.barcodeText}))
    }
  }
}
</script>

<style scoped>
#tab-switch {
  text-align: center;
}
#tab-switch > * {
  margin-right: 1.2rem;
}
#tab-switch > * {
  font-size: 2rem;
}
#result-wrapper {
  margin-top: 1rem;
}
#video-wrapper {
  margin-top: 12px;
  display: flex;
}
#video-info-board {
  padding: 4px 16px;
}
.text-strong {
  font-weight: 700;
}
.display-none {
  display: none;
}
a {
  text-decoration: none;
  color: inherit;
}
video {
  max-width: 1280px;
  max-height: 720px;
}
</style>
