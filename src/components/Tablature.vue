<template>
    <div class="tablature">
      <div v-for="(string, stringIndex) in tablature" :key="stringIndex">
        <span
          v-for="(fret, fretIndex) in string"
          :key="fretIndex"
          class="fret"
          :class="{ active: fretIndex === activeColumn }"
          @click="selectColumn(fretIndex)"
        >
          {{ fret }}
        </span>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    props: {
      tablature: {
        type: Array,
        required: true
      },
      activeColumn: {
        type: Number,
        required: true
      }
    },
    methods: {
      selectColumn(index) {
        this.$emit('column-selected', index);
      }
    }
  };
  </script>
  
  <style scoped>
  .tablature {
    font-family: monospace;
    margin-top: 20px;
  }
  .fret {
    display: inline-block;
    width: 20px;
    text-align: center;
    cursor: pointer;
  }
  .active {
    background-color: lightblue;
  }
  </style>
  