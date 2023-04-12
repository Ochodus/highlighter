<script setup>
import { watch, ref } from 'vue'
import TextField from './components/TextField.vue'


const pageNum = ref(0)

const sexTag = ref("pink")
const ageTag = ref("lightGreen")
const symptomTag = ref("yellow")
const diseaseTag = ref("lightBlue")
const backgroundTag = ref("lightGray")
const familyHistoryTag = ref("magenta")
const externalSourceTag = ref("orange")

const pickerModeArray = [sexTag, ageTag, symptomTag, diseaseTag, backgroundTag, familyHistoryTag, externalSourceTag]

const gradientColor = ref("linear-gradient(0deg, rgba(0, 0, 0, 1) 0%, rgba(0, 0, 0, 1) 100%)")

const mainKey = ref(0)
const pickerMode = ref(-1)

const textField = ref(null)

var isXslxLoaded = false

watch(
  sexTag,
  (cur, prev) => {
    console.log(cur + ", " + prev)
    textField.value.currentColor = cur
    textField.value.changeColor(cur, 0)
  }
)

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
  if (file != undefined) {
    reader.readAsText(file)
    isXslxLoaded = true
    mainKey.value++
  }
}

function exportAll() {
  textField.value.exportHighlighted(pickerModeArray)
}

function changeMode(mode)  {
  pickerMode.value = mode
  textField.value.currentColor = pickerModeArray[mode].value
  for (var i = 0; i < pickerModeArray.length; i++) {
    const target = document.getElementById("picker-" + i)
    
    if (i == mode) target.setAttribute("class", "picker selected")
    else target.setAttribute("class", "picker")
  }
  
  textField.value.changeMode(mode)
}

</script>

<template>
  <header>
    <div class="fileSelector">
      <input type="file" accept=".txt" v-on:change="importText" v-if="pageNum == 0"/>
    </div>
  </header>
  <div class="modeSelector" v-show="isXslxLoaded">
    <div class="picker" id="picker-0" @click="changeMode(0)"> 성별
      <color-picker v-model:pureColor="sexTag" v-model:gradientColor="gradientColor"/>
    </div>
    <div class="picker" id="picker-1" @click="changeMode(1)"> 나이
      <color-picker v-model:pureColor="ageTag" v-model:gradientColor="gradientColor"/>
    </div>
    <div class="picker" id="picker-2" @click="changeMode(2)"> 증상
      <color-picker v-model:pureColor="symptomTag" v-model:gradientColor="gradientColor"/>
    </div>
    <div class="picker" id="picker-3" @click="changeMode(3)"> 질병
      <color-picker v-model:pureColor="diseaseTag" v-model:gradientColor="gradientColor"/>
    </div>
    <div class="picker" id="picker-4" @click="changeMode(4)"> 기저질환
      <color-picker v-model:pureColor="backgroundTag" v-model:gradientColor="gradientColor"/>
    </div>
    <div class="picker" id="picker-5" @click="changeMode(5)"> 가족력
      <color-picker v-model:pureColor="familyHistoryTag" v-model:gradientColor="gradientColor"/>
    </div>
    <div class="picker" id="picker-6" @click="changeMode(6)"> 외부 자료
      <color-picker v-model:pureColor="externalSourceTag" v-model:gradientColor="gradientColor"/>
    </div>
  </div>
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

.modeSelector {
  width: auto;
  margin: auto;
  display: flex;
  padding: 5px;
}

.picker {
  background-color:aliceblue;
  border: solid 1px rgb(197, 214, 230);
  border-radius: 5px;
  margin-left: 5px;
  padding: 2px;
  box-shadow: 3px 0 5px #00000014;
  font-size: medium;
  font-weight: 800;
  font-family: "Gill Sans", sans-serif;
}

.picker.selected {
  background-color: rgb(145, 155, 163);
}

</style>
