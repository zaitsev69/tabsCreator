<template>
  <div id="app">
    <h1>Générateur de Tablature 🎸</h1>



    <GuitarFretboard @fret-selected="updateTablature" :frets="24" />

    <Tablature :tablature="tablature" :activeColumn="activeColumn" @column-selected="setActiveColumn" />

    <button @click="resetActiveColumn" class="reset-button">
      Réinitialiser la colonne sélectionnée
    </button>

    <button @click="resetAllColumns" class="reset-button reset-all">
      Réinitialiser toute la tablature
    </button>
  </div>
</template>

<script>
import GuitarFretboard from './components/GuitarFretboard.vue';
import Tablature from './components/Tablature.vue';

export default {
  components: {
    GuitarFretboard,
    Tablature
  },
  data() {
    return {
      tablature: Array(6).fill(Array(20).fill('-')), 
      activeColumn: 0 
    };
  },
  methods: {
    updateTablature({ string, fret }) {
      if (this.activeColumn === this.tablature[0].length - 1) {
        this.addNewColumn();
      }

      const newTablature = this.tablature.map((line, index) => {
        if (index === string - 1) {
          return line.map((value, i) => (i === this.activeColumn ? fret : value));
        }
        return line;
      });

      this.tablature = newTablature;
    },

    addNewColumn() {
      this.tablature = this.tablature.map((line) => [...line, '-']); 
    },

    resetActiveColumn() {
      const newTablature = this.tablature.map((line) =>
        line.map((value, i) => (i === this.activeColumn ? '-' : value))
      );
      this.tablature = newTablature;
    },

    resetAllColumns() {
      const initialLength = this.tablature[0].length;
      this.tablature = Array(6).fill(Array(20).fill('-'));
    },

  
    setActiveColumn(index) {
      this.activeColumn = index;
    }
  }
};
</script>

<style scoped>
#app {
  text-align: center;
  margin-top: 50px;
}

.reset-button {
  background-color: #f44336;
  color: white;
  border: none;
  padding: 10px 20px;
  margin: 10px;
  cursor: pointer;
}

.reset-button:hover {
  background-color: #d32f2f;
}

.reset-all {
  background-color: #4caf50;
}

.reset-all:hover {
  background-color: #388e3c;
}
</style>
