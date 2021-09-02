<template>
  <div class="home">
    <!-- <img alt="Vue logo" src="../assets/logo.png"> -->
    <input type="file" @change="onFileClick">
    <button @click="uploadImg">Upload</button>
  </div>
</template>

<script>
import { reactive } from '@vue/reactivity';
import { watch } from '@vue/runtime-core';
import {computed} from 'vue'
// @ is an alias to /src


export default {
  name: 'Home',
  components: {
  },
  setup() {

   const img = reactive({
      original: null,
      inputPreview: null,
      originalHeight: null,
      originalWidth: null,
      outPreview: null,
      test:null
   }) 

   function compressImage(image, scale, initalWidth, initalHeight){
    return new Promise((resolve) => {
        const canvas = document.createElement("canvas");

        canvas.width = scale * initalWidth;
        canvas.height = scale * initalHeight;

        const ctx = canvas.getContext("2d");

        ctx.drawImage(image, 0, 0, canvas.width, canvas.height);
        
        ctx.canvas.toBlob((blob) => {
            resolve(blob);
        }, "image/png"); 
    }); 
    }


    const getHeightAndWidthFromDataUrl = dataURL => new Promise(resolve => {
      const img = new Image()
      img.onload = () => {
        resolve({
          height: img.height,
          width: img.width
        })
      }
      img.src = dataURL
    })

    async function onFileClick(event) {

      img.original = event.target.files[0]
      
      if(img.original === null) {
        return
      }
      var imagePre = new Image()
      imagePre.src = URL.createObjectURL(img.original)
      img.inputPreview = imagePre

      getHeightAndWidthFromDataUrl(imagePre.src).then(dimensions => {
        img.originalWidth = dimensions.width
        img.originalHeight = dimensions.height
      })

    }

    const orgWidth = computed(() => img.originalWidth)
    const orgHeight = computed(() => img.originalHeight)
    

    watch([orgHeight, orgWidth], async function() {
        
        const MAX_WIDTH = 1000; //if we resize by width, this is the max width of compressed image
        const MAX_HEIGHT = 1000; //if we resize by height, this is the max height of the compressed image
      
        const widthRatioBlob = await compressImage(img.inputPreview, MAX_WIDTH / orgWidth.value, orgWidth.value, orgHeight.value); 
        // console.log(widthRatioBlob)
        const heightRatioBlob = await compressImage(img.inputPreview, MAX_HEIGHT / orgHeight.value,  orgWidth.value, orgHeight.value);

        const compressedBlob = widthRatioBlob.size > heightRatioBlob.size ? heightRatioBlob : widthRatioBlob;

        var imageOut = new Image()
        imageOut.src = URL.createObjectURL(compressedBlob)
        img.outPreview = imageOut

        URL.revokeObjectURL()
    })


    return {
      onFileClick,
      img
    }
    
  }
}
</script>
