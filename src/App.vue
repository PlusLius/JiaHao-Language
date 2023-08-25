
<script setup>
import * as monaco from "monaco-editor/esm/vs/editor/editor.api";
import "monaco-editor/esm/vs/basic-languages/javascript/javascript.contribution";
import { ref, onMounted } from "vue";
import JiaHao from './JiaHao'

const jiahao = new JiaHao()

const monacoEl = ref(null);
const resultEl = ref(null);

onMounted(() => {
  const jiahaoCode = `(class Point null
  (begin

    (def constructor (this x y)
      (begin
        (set (prop this x) x)
        (set (prop this y) y)))

    (def calc (this)
      (+ (prop this x) (prop this y)))))

(var p (new Point 10 20))

((prop p calc) p)

(class Point3D Point
  (begin

    (def constructor (this x y z)
      (begin
        ((prop (super Point3D) constructor) this x y)
        (set (prop this z) z)))

    (def calc (this)
      (+ ((prop (super Point3D) calc) this)
          (prop this z)))))

(var p (new Point3D 10 20 30))

((prop p calc) p)
  
`;

console.log();


  const editor = monaco.editor.create(monacoEl.value, {
    value: jiahaoCode,
    language: "javascript",
    theme: "vs-dark",
    automaticLayout: true,
  });

 editor.onDidChangeModelContent(e => {
  try {
    result.setValue(`${jiahao.evalParse(editor.getValue())}`)
  } catch (error) { }
  
})

  const result = monaco.editor.create(resultEl.value, {
    value: `${jiahao.evalParse(jiahaoCode)}`,
    language: "javascript",
    theme: "vs-dark",
    automaticLayout: true,
    readOnly: true,
  });
});
</script>

<template>
  <div>
    <div ref="monacoEl" @onkeyup="keyup" class="editor__monaco"></div>
    <div ref="resultEl" class="editor__result"></div>
  </div>
</template>

<style scoped>
.editor__monaco {
  float: left;
  width: 55vw;
  height: 100vh;
}
.editor__result {
  float: right;
  width: 45vw;
  height: 100vh;
}
</style>
