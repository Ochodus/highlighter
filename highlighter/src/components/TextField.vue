<script setup>
import { ref } from 'vue'

const importedText = ref("")
const textKey = ref(0)
const currentColor = ref("")

function getSelection(event) {
    console.log(event.target)
    const selection = document.getSelection()
    const selectedText = selection.toString()

    console.log(selection.anchorOffset)
    console.log(selection.focusOffset)
    if (selection.focusOffset - selection.anchorOffset != 0) {
        insertSpan(selection, selectedText)
    }
}

function insertSpan(selection, selectedText) {
    const span = document.createElement("SPAN")
    span.textContent = selectedText
    span.setAttribute("class", "highlighted")
    span.setAttribute("style", "background-color: " + currentColor.value)
    const range = selection.getRangeAt(0)
    range.deleteContents()
    range.insertNode(span);
    span.addEventListener("click", removeHighlight)
}

function removeHighlight(event) {
    const inner = event.target.innerHTML
    console.log(inner)
    event.target.remove()
    const selection = document.getSelection()
    const range = selection.getRangeAt(0)
    range.insertNode( document.createTextNode(inner) )
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

function changeMode(mode) {

}

defineExpose({
    importedText,
    textKey,
    currentColor,
    exportHighlighted,
    changeMode
})

</script>

<template>
    <div id = "text" :key="textKey">
        <span id="whole" @mouseup="getSelection($event)">{{ importedText }}</span>
    </div>
</template>

<style scoped>
#text {
    height: 100%;
}
</style>
