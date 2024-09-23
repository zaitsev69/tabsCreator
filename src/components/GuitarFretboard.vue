<template>
  <div class="fretboard-container">
    <div class="fretboard">
      <!-- Pour chaque corde -->
      <div
        v-for="(string, stringIndex) in strings"
        :key="stringIndex"
        class="string"
        :class="`string-${stringIndex + 1}`"
      >
        <!-- Case 0 -->
        <div class="fret" @click="selectFret(stringIndex, 0)">
          0
        </div>
        <!-- Boucle pour les frets -->
        <div
          v-for="fret in frets"
          :key="fret"
          class="fret"
          @click="selectFret(stringIndex, fret)"
        >
          {{ fret }}
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    frets: {
      type: Number,
      default: 24 // Nombre de frets
    }
  },
  data() {
    return {
      strings: [1, 2, 3, 4, 5, 6] 
    };
  },
  methods: {
    selectFret(stringIndex, fret) {
    
      this.$emit('fret-selected', { string: stringIndex + 1, fret });
    }
  }
};
</script>

<style scoped>
.fretboard-container {
  display: flex;
  justify-content: center;
  margin-top: 50px;
}

.fretboard {
  display: flex;
  flex-direction: column;
  gap: 10px;
  width: 100%;
  max-width: 900px; 
  position: relative;
}

.string {
  display: flex;
  align-items: center;
  justify-content: space-between;
  position: relative; 
}

.fret {
  width: 40px;
  height: 40px;
  background-color: #ccc;
  display: flex;
  justify-content: center;
  align-items: center;
  margin-right: 5px;
  cursor: pointer;
  border: 1px solid black;
  z-index: 1;
}

.fret:hover {
  background-color: white;
}

.string::before {
  content: '';
  position: absolute;
  top: 50%; 
  left: -50vw; 
  right: -50vw; 
  height: 1px;
  background-color: black;
  transform: translateY(-50%);
}

.string-1::before {
  height: 1px; 
}

.string-2::before {
  height: 2px;
}

.string-3::before {
  height: 3px;
}

.string-4::before {
  height: 4px;
}

.string-5::before {
  height: 5px;
}

.string-6::before {
  height: 6px;
}
</style>
