<template>
  <div class="fretboard-container">

    <div class="fretboard">
      <!-- Pour chaque corde -->
      <div v-for="(string, stringIndex) in strings" :key="stringIndex" class="string"
        :class="`string-${stringIndex + 1}`">
        <!-- Case 0 -->
        <div class="fret" @click="selectFret(stringIndex, 0)">
          0
        </div>
        <!-- Boucle pour les frets -->
        <div v-for="fret in frets" :key="fret" class="fret" @click="selectFret(stringIndex, fret)">
          {{ fret }}

        </div>
      </div>
    </div>
  </div>
  <button @click="addSymbol('/')" class="action-button">Slide /</button>
  <button @click="addSymbol('b')" class="action-button">Bend b</button>
  <button @click="addSymbol('p')" class="action-button">Pull-off p</button>
  <button @click="addSymbol('h')" class="action-button">Hammer-on h</button>
  <button @click="muteColumn" class="action-button">Ghost note x</button>
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
      strings: [1, 2, 3, 4, 5, 6],
      currentSymbol: '', // Le symbole ajouter à la note sélectionnée
      firstFret: null,
    };
  },
  methods: {
    selectFret(stringIndex, fret) {
      if (this.currentSymbol && this.firstFret === null) {
        // stocker la premiere note lors de la selection d'un symbole
        this.firstFret = fret;
      } else if (this.currentSymbol && this.firstFret !== null) {
        // ajoute le symbole avec les deux notes
        this.$emit('fret-selected', {
          string: stringIndex + 1,
          fret: `${this.firstFret}${this.currentSymbol}${fret}`,
        });
        this.firstFret = null;
        this.currentSymbol = '';
      } else {
        // si pas de symbole choisi, sélectionnez la note
        this.$emit('fret-selected', { string: stringIndex + 1, fret });
      }
    },
    addSymbol(symbol) {
      this.currentSymbol = symbol; // défini le symbole choisi
    },
    muteColumn() {
      this.$emit('mute-column');
    }
  },

};

</script>

<style scoped>
.fretboard-container {
  display: flex;
  justify-content: center;
  margin-top: 10px;
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
  border-radius: 3px;
  background-color: #ccc;
  display: flex;
  justify-content: center;
  align-items: center;
  margin-right: 5px;
  cursor: pointer;
  border: 1px solid black;
  z-index: 1;
  user-select: none !important;


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
  background-color: #343a40;
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


.action-button {
  background-color: #2c2c2c;
  color: white;
  border: none;
  padding: 10px 20px;
  margin: 10px;
  cursor: pointer;
  font-family: "Poppins", sans-serif;
  border-radius: 5px;
  box-shadow: rgba(0, 0, 0, 0.35) 0px 5px 15px;
}

.action-button:hover {
background-color: black;
}
</style>
