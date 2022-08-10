<script setup lang="ts">
import { ref } from 'vue';
import { AdbDaemonWebUsbDeviceManager } from '@yume-chan/adb-daemon-webusb';
import { device } from '../hooks/useRef.ts'

const notification = useNotification()
const linkState = ref('link')

async function handleLinkDevice() {
  try {
    device.value = await navigator.usb.requestDevice({
      filters: [
        {
          classCode: 0xff,
          subclassCode: 0x42,
          protocolCode: 1,
        },
      ],
    });
    linkState.value = 'linked'
    notification.success({
      title: '设备连接成功',
      content: `ADB ID: ${device.value.serialNumber}`,
      duration: 2000,
    })
  } catch (e) {
    if (e instanceof DOMException && e.name === "NotFoundError") {
      linkState.value = 'error'
      notification.error({
        type: 'error',
        title: '已取消设备授权',
        duration: 2000,
      })
      return;
    }
    throw e;
  }
}
</script>

<template>
  <n-flex vertical>
    <n-alert v-if="linkState === 'link'" title="尚未连接任何 HarmonyOS 设备" type="info">
      <n-button type="info" @click="handleLinkDevice">
        连接设备
      </n-button>
    </n-alert>
    <n-alert v-if="linkState === 'error'" title="设备连接失败" type="error">
      可能是您拒绝了我们发出的浏览器权限请求，或是您的设备/浏览器不支持我们需要的 USB 功能。
      <br>
      如果您的浏览器没有任何提示，您可以使用您的 PC 及基于 Chromium 较新版本的现代浏览器（如 Google Chrome 与 Microsoft Edge）再次尝试。
      <br>
      <n-button type="error" style="margin-top: 8px;" @click="handleLinkDevice">
        重新尝试连接设备
      </n-button>
    </n-alert>
    <n-alert v-if="linkState === 'linked'" title="已连接设备" type="success">
      您的设备 ADB ID: <code>{{ device.serialNumber }}</code>
      <br>
      您可以尽情享用 HomoOS Web 工具箱内的功能了。
      <n-collapse>
        <n-collapse-item title="WebUSB API 调试信息">
          vendorId: {{ device.vendorId }} <br>
          productId: {{ device.productId }} <br>
          serialNumber: {{ device.serialNumber }} <br>
        </n-collapse-item>
      </n-collapse>
    </n-alert>
  </n-flex>
</template>