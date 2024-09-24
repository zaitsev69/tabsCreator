<template>
  <div class="tablature">
    <div v-for="(string, stringIndex) in tablature" :key="stringIndex">
      <span v-for="(fret, fretIndex) in string" :key="fretIndex" class="fret"
        :class="{ active: fretIndex === activeColumn }" :style="{ width: calculateColumnWidth(fretIndex) }"
        @click="selectColumn(fretIndex)">
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
    },
    calculateColumnWidth(columnIndex) {
      const columnData = this.tablature.map((string) => string[columnIndex]);

      // vérifie si symbole ou non
      const hasComplexNotation = columnData.some(fret =>
        /[ph\/b]/.test(fret)
      );

      // si symbole élargi la collone
      return hasComplexNotation ? '40px' : '20px';  
    },

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
  transition: width 0.2s ease;
}

.active {
  background-color: lightblue;
}
</style>