<template>
  <div>
    <h1>swagger-markdown UI</h1>
    <p>
      A simple UI for
      <a
        href="https://github.com/syroegkin/swagger-markdown"
        target="_blank"
        rel="noopener"
        >swagger-markdown</a
      >
      - to convert swagger yaml to markdown.
    </p>
    <h3>Input Swagger Yaml here:</h3>
    <textarea id="yaml" rows="16" cols="100" v-model="inputYaml"> </textarea>
    <br />
    <button v-on:click="handleSubmit">Convert!</button>
    <h3>Output:</h3>
    <textarea
      id="markdown"
      rows="16"
      cols="100"
      v-model="outputMarkdown"
      readonly
    >
    </textarea>
  </div>
</template>

<script>
const yaml = require('js-yaml');
const transformInfo = require('../swagger-markdown/transformers/info');
const transformPath = require('../swagger-markdown/transformers/path');
const transformSecurityDefinitions = require('../swagger-markdown/transformers/securityDefinitions');
const transformExternalDocs = require('../swagger-markdown/transformers/externalDocs');
const transformDefinition = require('../swagger-markdown/transformers/definitions');
export default {
  name: 'HelloWorld',
  data: function() {
    return {
      inputYaml: 'Insert sample yaml here',
      outputMarkdown: 'Output sample markdown will be here'
    };
  },
  methods: {
    handleSubmit: function() {
      try {
        const inputDoc = yaml.safeLoad(this.inputYaml);
        const document = [];
        if ('info' in inputDoc) {
          document.push(transformInfo(inputDoc.info));
        }

        if ('externalDocs' in inputDoc) {
          document.push(transformExternalDocs(inputDoc.externalDocs));
        }

        // Security definitions
        if ('securityDefinitions' in inputDoc) {
          document.push(
            transformSecurityDefinitions(inputDoc.securityDefinitions)
          );
        }

        // Process Paths
        if ('paths' in inputDoc) {
          Object.keys(inputDoc.paths).forEach(path =>
            document.push(transformPath(path, inputDoc.paths[path], {}))
          );
        }
        // Models (definitions)
        if ('definitions' in inputDoc) {
          document.push(transformDefinition(inputDoc.definitions));
        }

        this.outputMarkdown = document.join('\n');
      } catch (e) {
        this.outputMarkdown = e;
      }
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
