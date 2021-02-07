<template>
  <div class="barcode">
    <h1>Page Barcode</h1>
    <input v-model="barcodeValue" /><br /><br />
    <h3>Barcode</h3>
    <!-- https://www.npmjs.com/package/vue-barcode -->
    <barcode :value="barcodeValue">
      Show this if the rendering fails.
    </barcode>
    <hr />
    <h3>QR Barcode</h3>
    <!-- https://www.npmjs.com/package/vue-qrcode -->
    <barcode2 :value="barcodeValue" />
  </div>
</template>

<script>
import VueBarcode from "vue-barcode";
import VueQrcode from "vue-qrcode";

export default {
  name: "Barcode",
  components: {
    barcode: VueBarcode,
    barcode2: VueQrcode
  },
  data() {
    return {
      barcodeValue: "test"
    };
  }
};
</script>
