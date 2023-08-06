<template>
  <div>
    <PdfViewer :pdf-base64="pdfBase64"></PdfViewer>
  </div>
</template>
<script scoped>
import PdfViewer from '@/components/PdfViewer.vue'
import {pdf as pdfBase64} from '@/assets/base64'

export default{
  components: { PdfViewer },
  data () {
    return {
      pdfBase64
    }
  }
}
</script>