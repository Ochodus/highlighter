<script setup>
import { ref } from 'vue'
import TextField from './components/TextField.vue'

const pageNum = ref(0)
const lastPageNum = 3

const mainKey = ref(0)

const textField = ref(null)

var isXslxLoaded = false

function importText(e) {
  var file = e.target.files[0]
  const reader = new FileReader()
  reader.onload = (evt) => {
    if (evt.target.readyState === FileReader.DONE) {
      console.log(evt.target.result)
      textField.value.importedText = evt.target.result
      textField.value.textKey++
    }
  }
  reader.readAsText(file)
  isXslxLoaded = true
  mainKey.value++
}

function exportAll() {
  textField.value.exportHighlighted()
}

</script>

<template>
  <header>
    <div class="fileSelector">
      <input type="file" accept=".txt" v-on:change="importText" v-if="pageNum == 0"/>
    </div>
  </header>
  <div class="inputField" :key="mainKey" v-show="isXslxLoaded">
    <TextField ref="textField" v-show="pageNum == 0"></TextField>
  </div>
  <div class="footer" v-show="isXslxLoaded">
    <button id="export" @click="exportAll()" v-if="pageNum == 0">
      내보내기
    </button>
  </div>
</template>

<style scoped>
header {
  padding: 20px;
  line-height: 1;
  height: 200px;
  width: 100%;
  text-align: center;
  display: flex;
}

.fileSelector {
  margin: auto;
}

.inputField {
  border-top: solid;
  border-bottom: solid;
  border-radius: 10px;
  overflow-y: auto;
}

.footer {
  width: 100%;
  height: 110px;
  bottom: 0px;
}

#next, #confirm {
  float:right;
}
</style>
