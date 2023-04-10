<script setup>
import { ref } from 'vue'

const importedText = ref("")
const textKey = ref(0)
const currentColor = ref("")
const pickerMode = ref(-1)

function getSelection(event) {
    const selection = document.getSelection()
    if (!selection.isCollapsed && pickerMode.value != -1) {
        insertSpan(selection)
    }
}

function insertSpan(selection) {
    const span = document.createElement("SPAN")
    span.textContent = selection.toString()
    
    var mode = pickerMode.value
    var color = currentColor.value

    span.setAttribute("class", "highlighted-" + pickerMode.value)
    span.setAttribute("style", "background-color: " + currentColor.value)

    const range = selection.getRangeAt(0)
    range.deleteContents()

    range.insertNode(span)

    span.addEventListener("click", (event) => {
        const innerSelection = document.getSelection()
        if (innerSelection.isCollapsed) {
            removeHighlight(event, innerSelection)
        } 
        else {
            divideHighlight(event, selection, innerSelection)
        }   
    })
}


function removeHighlight(event, selection) {
    const innerContent = event.target.innerHTML
    event.target.remove()
    
    const startPoint = selection.getRangeAt(0)
    startPoint.insertNode( document.createTextNode(innerContent) )
}

function divideHighlight(event, selection, innerSelection) {
    const inner = event.target.innerHTML 
    
    console.log(event.target)
    const child = event.target.childNodes
            
    const pspan = document.createElement("SPAN")
    const bspan = document.createElement("SPAN")
    pspan.textContent = child[0].textContent
    bspan.textContent = child[2].textContent
            
    range.deleteContents()

    pspan.setAttribute("class", "highlighted-" + mode)
    pspan.setAttribute("style", "background-color: " + color)
    bspan.setAttribute("class", "highlighted-" + mode)
    bspan.setAttribute("style", "background-color: " + color)
            
    range.insertNode(bspan)
            
    var brange = new Range()
    brange.setStart(bspan, 0)

    var prange = new Range()
    prange.setStart(pspan, 0)

    bspan.addEventListener("click", (event) => {
        
        removeHighlight(event, brange, mode, color)    
    })
    range.insertNode(child[1])
            
    range.insertNode(pspan)
    pspan.addEventListener("click", (event) => {
        
        removeHighlight(event, prange, mode, color)    
    })
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
