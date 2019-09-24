<template>
  <div class="group">
    <p v-html="line1"></p>
    <p>{{ line2 }}</p> 
    <p v-html="diff"></p>  
    <p v-html="range"></p>        
  </div>
</template>

<script>
import { diff_match_patch } from 'diff-match-patch';

let dmp = new diff_match_patch();

export default {  
  name: 'SentenceGroup',
  props: {
    sentence: Array
  },
  data: () => {
    return {
      line1: '',
      line2: '',
      diff: '',
      range: '',
    }
  },
  created: function () {
    this.highlight();
  },
  watch: {
    sentence: function () {
      this.highlight();
    }
  },
  methods: {
    highlight: function () {
      this.line1 = this.sentence[0] || '';
      this.line2 = this.sentence[1] || '';

      let diff = dmp.diff_main(this.line1, this.line2);

      let range = [];

      let lastIndex = 0;
      let firstIndex = 0;

      let tempStr = [];
      let tempDiff = [];
      let diffDict = {};

      diff.forEach((item, index) => {
        if (item[0] === 0) {
            tempStr.push(item[1]);
            tempDiff.push(item);
        } else {
          if (index - 1 >= 0 && diff[index - 1][0] === -1) {
            // do nothing
          } else {
            tempStr.push(item[1]);
            tempDiff.push(item);
          }
        }
      });

      let newStr = tempStr.join('')

      tempDiff.forEach((item) => {
        if (item[0] === 0) {
          lastIndex += item[1].length;
        } else {
          lastIndex = newStr.indexOf(item[1], lastIndex) + item[1].length;
        }
        range.push([item[0], firstIndex, lastIndex]);
        firstIndex = lastIndex;
      });

      let result = '';
      range.forEach(item => {
        let sub = newStr.substring(item[1], item[2]).replace(/\s/, '&nbsp');
        if (item[0]) {
          if (!sub) {
            sub = '&nbsp'.repeat(item[2] - item[1]);
          }
          sub = `<span class="${item[0] === 1 ? 'insert' : 'delete'}">${sub}</span>`;
        }
        result += sub;
      });

      this.line1 = result;
      this.diff = JSON.stringify(tempDiff); 
      this.range = JSON.stringify(range);
    }
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.insert {
  background: #42b983;
}
.delete {
  background: #f39c12;
}
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
