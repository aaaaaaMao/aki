<template>
  <div id="app">
    <div id="left-pane">
      <textarea id="input" v-model="input" placeholder="input phrases"></textarea>
      <button id="ok" v-on:click="clean">清除</button>
    </div>
    <div id="left-pane" v-for="sentence in sentences">
      <SentenceGroup 
      v-bind:sentence="sentence"
      ></SentenceGroup>
    </div>
  </div>
</template>

<script>
import SentenceGroup from './components/SentenceGroup.vue'
import * as _ from 'lodash'

export default {
  name: 'app',
  data: () => {
    return {
      input: '',
      sentences: []
    }
  },
  methods: {
    splitInput: function () {
      this.sentences = this.input.split(/\r\n|\r|\n/);
      _.remove(this.sentences, (s) => {
        return s === '';
      });
      this.sentences = _.chunk(this.sentences, 2);
    },
    clean: function () {
      this.input = '';
    }
  },
  watch: {
    input: function () {
      this.debouncedGetAnswer();
    }
  },
  created: function () {
    this.debouncedGetAnswer = _.debounce(this.splitInput, 500);
  },
  components: {
    SentenceGroup
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
#left-pane {
  width: 50%;
  float: left;
}
#right-pane {
  width: 50%;
  float: right;
}
#input {
  width: 100%;
  height: 800px;
}
.insert {
  background: #42b983;
}
.delete {
  background: #f39c12;
}
</style>
