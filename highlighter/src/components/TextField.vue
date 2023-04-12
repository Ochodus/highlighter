<script setup>
import { ref } from 'vue'

const importedText = ref("")
const textKey = ref(0)
const currentColor = ref("")
const pickerMode = ref(-1)

function getSelection() {
    const selection = document.getSelection()
    const range = selection.getRangeAt(0)
    if (!selection.isCollapsed && pickerMode.value != -1) {
        insertSpan(range)
    }
}

function insertSpan(range, mode=null, color=null) {
    const span = document.createElement("SPAN")
    span.textContent = range.toString()
    
    if (mode == null) mode = pickerMode.value
    if (color == null) color = currentColor.value

    span.setAttribute("class", "highlighted-" + mode)
    span.setAttribute("style", "background-color: " + color)

    range.deleteContents()

    range.insertNode(span)

    span.addEventListener("click", (event) => {
        const innerSelection = document.getSelection()
        
        if (innerSelection.isCollapsed) {
            removeHighlight(event, range)
        } 
        else {
            const innerRange = innerSelection.getRangeAt(0)
            divideHighlight(range, innerRange, mode, color)
        }   
    })
    span.addEventListener("mouseup", (event) => {
        getSelection(event)
    })
}


function removeHighlight(event, range) {
    const innerContent = event.target.innerHTML
    console.log(range.toString())

    while (innerContent != range.toString()) {
        range.setStart(range.startContainer, range.startOffset + 1)
    }
        
    event.target.remove()

    range.insertNode( document.createTextNode(innerContent) )
}

function divideHighlight(range, innerRange, mode, color) {
    
    console.log(range)
    console.log(innerRange)

    var prevRange = innerRange.cloneRange()
    var midRange = innerRange.cloneRange()
    var nextRange = innerRange.cloneRange()

    nextRange.setStart(innerRange.startContainer, innerRange.endOffset)
    nextRange.setEnd(range.endContainer, range.endOffset)

    insertSpan(nextRange, mode, color)

    midRange.setStart(innerRange.startContainer, innerRange.startOffset)
    midRange.setEnd(range.endContainer, range.endOffset)

    insertSpan(midRange)

    prevRange.setStart(range.startContainer, range.startOffset)
    prevRange.setEnd(range.endContainer, range.endOffset)
    
    insertSpan(prevRange, mode, color)

    nextRange.setStart(nextRange.startContainer, nextRange.startOffset + 2)
    midRange.setStart(midRange.startContainer, midRange.startOffset + 1)

    console.log(prevRange.toString())
    console.log(midRange.toString())
    console.log(nextRange.toString())
}

function exportHighlighted(pickerModeArray) {
    const whole = document.getElementById("whole")
    const text = whole.innerHTML

    var output = {
        colorInfo: pickerModeArray,
        text: text
    }

    const filename = "highlighted"
    const element = document.createElement('a');

    element.setAttribute('href', 'data:text/plain;charset=utf-8,' + encodeURIComponent(JSON.stringify(output)));
    element.setAttribute('download', filename);
    document.body.appendChild(element)
    element.click();
    console.log(whole)
}

function changeMode(mode) {
    pickerMode.value = mode
}

function changeColor(color, mode) {
    var doms = document.getElementsByClassName("highlighted-" + mode)
    for (var i of doms) {
        i.setAttribute("style", "background-color: " + color)
    }
}

defineExpose({
    importedText,
    textKey,
    currentColor,
    exportHighlighted,
    changeMode,
    changeColor
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
