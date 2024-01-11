<script setup lang="ts">
import WelcomeItem from './WelcomeItem.vue'
import DocumentationIcon from './icons/IconDocumentation.vue'
import { create } from 'kubo-rpc-client'
import {ref} from "vue";
import Buffer from "vue-buffer";
const ipfs = await create({host:'61.172.179.4',port: 5001})

const selectedFile = ref()

const handleFileUpload = function (event) {
    selectedFile.value = event.target.files[0];
}

const upload = async function (){
    console.log("upload")
    if (selectedFile.value) {
        const fileReader = new FileReader();

        fileReader.onload = async () => {
            const fileBuffer = Buffer.from(fileReader.result);

            try {
                const { cid } = await ipfs.add(fileBuffer,{
                    progress: (pong) => {console.log(`receive : ${pong}`)}
                });
                console.log('File uploaded to IPFS. IPFS Hash:', cid.toString());
            } catch (error) {
                console.error('Error uploading to IPFS:', error);
            }
        };

        fileReader.readAsArrayBuffer(selectedFile.value);
    } else {
        console.warn('No file selected for upload.');
    }
}
</script>

<template>
  <WelcomeItem>
    <template #icon>
      <DocumentationIcon />
    </template>
    <template #heading>IPFS 上传demo</template>

      <input type="file"  @change="handleFileUpload"/>
      <button @click="upload">上传</button>
  </WelcomeItem>

</template>
