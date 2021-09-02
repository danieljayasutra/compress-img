<template>
  <div class="about">
    <h1>This is an about page</h1>
    <input type="file" accept="image/*" @change="getFile">
    <div>
        <img id="input-preview" alt="This is the preview of the image you are going to upload."/>
    </div>
    <div>
        <img id="output-preview" alt="This is the compressed result of the image you will upload."/>  
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      img: null,
      in: null,
      out: null,
      file: null,
    }
  },
  methods: {
    getFile(event) {
      const file = event.target.files[0]
      this.file = file
      this.runFn(file)
    },
    doDrawing(file) {
      return new Promise((resolve) => {
        const canvas = document.createElement("canvas")
        canvas.width = 300
        canvas.height = 300
        
        const inputPreview = document.getElementById("input-preview");

        inputPreview.src = URL.createObjectURL(file)
        const ctx = canvas.getContext("2d")

        ctx.drawImage(inputPreview, 0, 0, canvas.width, canvas.height)

        ctx.canvas.toBlob((blob) => {
            resolve(blob);
          }, "image/png"); 
        })
    },
    async runFn(file) {
      const blog = await this.doDrawing(file)
      console.log(blog)
    }
  },
  created() {
    
  }
}
</script>
