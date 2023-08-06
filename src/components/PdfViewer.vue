<template>
    <div class="canvas-container">
        <canvas ref="the-canvas" class="the-canvas"></canvas>
        <div class="page-controller">
          <span @click="prePage">上一页</span>
          <span>第 <input v-model="numPageInput" @blur="inputPageNum" class="num-page"/> / {{ numPages }} 页</span>
          <span @click="nextPage">下一页</span>
        </div>
    </div>
  </template>
  <script scoped>
  import * as pdfjsLib from "pdfjs-dist";
  import "pdfjs-dist/build/pdf.worker.entry"
  
  pdfjsLib.GlobalWorkerOptions.workerSrc = window.pdfjsWorker
  
  export default{
    props: {
      pdfBase64: String,
      pdfUrl: String
    },
    data () {
      return {
        pdf: null,
        numPages: 0,
        numPage: 0,
        numPageInput: 0
      }
    },
    mounted () {
      const that = this
  
      let loadingTask
      if (this.pdfUrl) {
        loadingTask = pdfjsLib.getDocument({
          url: this.pdfUrl
        })
      } else if (this.pdfBase64) {
        loadingTask = pdfjsLib.getDocument({
          data: atob(this.pdfBase64)
        })
      } else {
        return
      }
  
      loadingTask.promise.then(function(pdf) {
        that.pdf = pdf
        that.numPages = pdf.numPages
        if (pdf.numPages > 0) {
          that.numPage = 1
          that.numPageInput = 1
        }
      });
    },
    watch: {
      numPage (newVal) {
        this.numPageInput = newVal
        this.renderPage(newVal)
      }
    },
    methods: {
      inputPageNum () {
        let newVal = this.numPageInput
        let oldVal = this.numPage
        if (newVal === oldVal) {
          return
        }
        newVal = parseInt(newVal)
        if (!Number.isInteger(newVal)) {
          return
        }
        if (newVal < 1 || newVal > this.numPages) {
          return
        }
        this.numPage = newVal
      },
      nextPage () {
        if (this.numPage === this.numPages) {
          return
        }
        this.numPage++
      },
      prePage () {
        if (this.numPage === 1) {
          return
        }
        this.numPage--
      },
      renderPage(numPage){
        const that = this
        this.pdf.getPage(numPage).then(function(page) {
          let viewport = page.getViewport({ scale: 1, });
          
          let outputScale = window.devicePixelRatio || 1;
          let transform = outputScale !== 1
          ? [outputScale, 0, 0, outputScale, 0, 0]
          : null;
  
          let canvas = that.$refs['the-canvas']
          let context = canvas.getContext('2d');
          canvas.width = Math.floor(viewport.width * outputScale);
          canvas.height = Math.floor(viewport.height * outputScale);
          
          let renderContext = {
              canvasContext: context,
              transform: transform,
              viewport: viewport
          };
          page.render(renderContext);
        });
      }
    }
  }
  </script>
  <style scoped>
  .the-canvas{
    width: 100%;
    height: auto;
    border: 1px solid grey;
  }
  
  .page-controller{
    display: flex;
    justify-content: space-between;
  }
  
  .num-page{
    width: 4em;
  }
  </style>