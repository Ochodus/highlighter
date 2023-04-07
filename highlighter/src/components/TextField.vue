<script setup>
import { ref } from 'vue'

const importedText = ref("")
const textKey = ref(0)

function getSelection() {
    console.log("eee")
    const selection = document.getSelection()
    const selectedText = selection.toString()
    console.log(selectedText)

    insertSpan(selection, selectedText)
}

function insertSpan(selection, selectedText) {
    const span = document.createElement("SPAN")
    console.log(span)
    span.textContent = selectedText
    span.setAttribute("class", "yellow-highlight")
    span.setAttribute("style", "background-color: yellow")
    const range = selection.getRangeAt(0)
    range.deleteContents()
    range.insertNode(span);
}

function exportHighlighted() {
    const whole = document.getElementById("whole")

    const text = whole.innerHTML
    
    const filename = "highlighted"
    const element = document.createElement('a');
    element.setAttribute('href', 'data:text/plain;charset=utf-8,' + encodeURIComponent(text));
    element.setAttribute('download', filename);
    document.body.appendChild(element)
    element.click();
    console.log(whole)
}

defineExpose({
    importedText,
    textKey,
    exportHighlighted
})

</script>

<template>
    <div id = "text" :key="textKey">
        <span id="whole" @mouseup="getSelection()">{{ importedText }}</span>
    </div>
</template>

<style scoped>
#text {
    height: 100%;
}

.yellow-highlight {
    background-color: yellow;
}
</style>
