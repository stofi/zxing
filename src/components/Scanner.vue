<template>
  <div>
    <div class="flex justify-between gap-2">
      <button
        :disabled="!selectedDeviceId"
        :class="!selectedDeviceId ? 'opacity-20' : ''"
        class="px-2 py-1 bg-blue-200 rounded"
        id="startButton"
        @click="startButton"
      >
        Start
      </button>
      <button class="px-2 py-1 bg-blue-200 rounded" @click="reset">
        Reset
      </button>
      <div class="flex-1"></div>
      <div class="flex-1">
        <label for="device" class="block text-sm font-medium text-gray-700"
          >Select camera</label
        >
        <select
          v-model="selectedDeviceId"
          id="device"
          name="device"
          class="block w-full py-2 pl-3 pr-10 mt-1 text-base border-gray-300 rounded-md focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
        >
          <option
            v-for="device in videoDevices"
            :value="device.deviceId"
            :key="device.deviceId"
          >
            {{ device.label }}
          </option>
        </select>
      </div>
    </div>
    <div class="mt-2">
      <div>Result:</div>
      <pre class="h-12 p-2 mb-2 rounded bg-slate-300">{{ result }}</pre>
    </div>
    <div
      class="flex items-center justify-center max-w-full overflow-hidden rounded h-80"
    >
      <video
        class="bg-gradient-to-br from-blue-500 to-red-500 max-w-none w-[600vw] h-[600vw]"
        id="video"
      ></video>
    </div>
  </div>
</template>
<script lang="ts">
let codeReader: BrowserMultiFormatReader

import {
  BrowserMultiFormatReader,
  NotFoundException,
  BarcodeFormat,
  DecodeHintType,
} from '@zxing/library'
import { defineComponent } from 'vue'
export default defineComponent({
  name: 'Scanner',
  data() {
    return {
      selectedDeviceId: null as string | null,
      videoDevices: [] as { deviceId: string; label: string }[],
      result: '',
    }
  },
  async mounted() {
    const formats = [BarcodeFormat.DATA_MATRIX, BarcodeFormat.QR_CODE]

    const hints = new Map()

    hints.set(DecodeHintType.POSSIBLE_FORMATS, formats)
    codeReader = new BrowserMultiFormatReader(hints)
    const videoInputDevices = await codeReader.listVideoInputDevices()

    this.videoDevices = videoInputDevices.map(({ label, deviceId }) => ({
      label,
      deviceId,
    }))
  },
  methods: {
    reset() {
      this.result = ''
    },

    startButton() {
      codeReader.decodeFromVideoDevice(
        this.selectedDeviceId,
        'video',
        (result, err) => {
          if (result) {
            console.log(result)
            this.result = result.getText()
          }
          if (err && !(err instanceof NotFoundException)) {
            console.error(err)
            this.result = err.message
          }
        }
      )

      console.log(
        `Started continous decode from camera with id ${this.selectedDeviceId}`
      )
    },
  },
})
</script>
