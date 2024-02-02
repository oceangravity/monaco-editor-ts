<script setup lang="ts">
import { computed, onMounted, ref } from 'vue';
import * as monaco from 'monaco-editor';
import { isNil } from 'lodash-es';

export interface Props {
  language?: string;
  readOnly?: boolean;
  value?: string;
  options?: monaco.editor.IStandaloneEditorConstructionOptions;
}

const props = withDefaults(defineProps<Props>(), {
  options: () => ({}),
});

const manocaEditorRef = ref();
const editor = ref<monaco.editor.IStandaloneCodeEditor>();
onMounted(() => {
  // validation settings
  monaco.languages.typescript.javascriptDefaults.setDiagnosticsOptions({
    noSemanticValidation: true,
    noSyntaxValidation: false,
  });

  // compiler options
  monaco.languages.typescript.javascriptDefaults.setCompilerOptions({
    target: monaco.languages.typescript.ScriptTarget.ES2015,
    allowNonTsExtensions: true,
  });

  // extra libraries
  var libSource = [
    'declare class Facts {',
    '    /**',
    '     * Returns the next fact',
    '     */',
    '    static next():string',
    '}',
  ].join('\n');
  var libUri = 'ts:filename/facts.d.ts';
  monaco.languages.typescript.javascriptDefaults.addExtraLib(libSource, libUri);
  // When resolving definitions and references, the editor will try to use created models.
  // Creating a model for the library allows "peek definition/references" commands to work with the library.
  monaco.editor.createModel(libSource, 'typescript', monaco.Uri.parse(libUri));

  var jsCode = [
    '"use strict";',
    '',
    'class Chuck {',
    '    greet() {',
    '        return Facts.next();',
    '    }',
    '}',
  ].join('\n');

  editor.value = monaco.editor.create(manocaEditorRef.value, {
    value: jsCode,
    language: 'javascript',
  });
});

defineExpose({
  monaco,
  editor,
});
</script>

<template>
  <div ref="manocaEditorRef" class="vue-monaco-editor" />
</template>

<style>
.vue-monaco-editor {
  width: 100%;
  height: 100%;
  border: 1px solid rgba(0, 0, 0, 0.1);
  box-shadow: 0 1px 2px 0 rgb(0 0 0 / 0.05);
}
</style>
