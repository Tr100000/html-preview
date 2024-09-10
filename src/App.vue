<script setup lang="ts">
import loader from "@monaco-editor/loader";
import { emmetHTML } from "emmet-monaco-es";
import { editor } from "monaco-editor";
import { useMonacoEx } from "monaco-editor-ex";
import { onMounted, onUnmounted, useTemplateRef } from "vue";

let monacoEditor: editor.IStandaloneCodeEditor;

const domPreview = useTemplateRef("preview");

const startValue = `
<h1>HTML Realtime Preview</h1>
<p>Edit HTML on the left, see changes on the right!</p>

<button onclick="alert('I was clicked!')">Click Me!</button>

<style>
  :root {
    font-family: Arial, Helvetica, sans-serif;
  }
</style>
`.substring(1);

onMounted(() => {
  loader.init().then((monaco) => {
    emmetHTML(monaco);
    useMonacoEx(monaco);

    let model = monaco.editor.createModel(startValue, "html");

    monacoEditor = monaco.editor.create(document.getElementById("editor")!, {
      model: model,
      theme: "vs",
      tabSize: 2,
      automaticLayout: true,
    });

    monacoEditor.onDidChangeModelContent(updatePreview);
    updatePreview();
  });
});

onUnmounted(() => {
  if (monacoEditor) {
    monacoEditor.dispose();
  }
});

function updatePreview() {
  if (monacoEditor) {
    domPreview.value!.srcdoc = monacoEditor.getValue();
  }
}
</script>

<template>
  <div id="container">
    <div id="editor"></div>
    <iframe id="preview" ref="preview" title="Preview"></iframe>
  </div>
</template>

<style scoped>
#container {
  display: flex;
}

#editor,
#preview {
  text-align: left;
  width: calc(50vw - 16px);
  height: calc(100vh - 16px);
  padding: 2px;
  margin: 8px;
  border: 1px solid black;
  border-radius: 2px;
}
</style>
