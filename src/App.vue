<template>
  <div id="app">
    <h1>Générateur de Tablature 🎸</h1>



    <GuitarFretboard @fret-selected="updateTablature" @mute-column="muteActiveColumn" :frets="24" />

    <Tablature :tablature="tablature" :activeColumn="activeColumn" @column-selected="setActiveColumn" />

    <button @click="resetActiveColumn" class="reset-button">
      Réinitialiser la colonne sélectionnée
    </button>

    <button @click="resetAllColumns" class="reset-button reset-all">
      Réinitialiser toute la tablature
    </button>

    <button @click="addSpace" class="add-space-button">Ajouter un espace</button>

    <button @click="undo" class="undo-button" :disabled="history.length === 0">↻</button>

    <button @click="exportAsText" class="export-button">Exporter en .txt</button>
    <!--<button @click="exportAsPDF" class="export-button">Exporter en .pdf</button>-->
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
      activeColumn: 0,
      history: []
    };
  },
  methods: {
    updateTablature({ string, fret }) {
      this.saveHistory();
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

    addSpace() {
      this.tablature = this.tablature.map((line) => {
        const newLine = [...line];
        newLine.splice(this.activeColumn + 1, 0, '-');
        return newLine;
      });
    },

    resetActiveColumn() {
      this.saveHistory(); // sauvegarde de l'état avant réinitialisation
      const newTablature = this.tablature.map((line) =>
        line.map((value, i) => (i === this.activeColumn ? '-' : value))
      );
      this.tablature = newTablature;
    },

    resetAllColumns() {
      this.saveHistory(); 
      const initialLength = this.tablature[0].length;
      this.tablature = Array(6).fill(Array(20).fill('-'));
    },


    setActiveColumn(index) {
      this.activeColumn = index;
    },

    saveHistory() {
      const savedState = JSON.stringify(this.tablature);
      this.history.push(savedState);
    },

    undo() {
      if (this.history.length > 0) {
        const previousState = this.history.pop();
        this.tablature = JSON.parse(previousState);
      }
    },
    muteActiveColumn() {
      const newTablature = this.tablature.map((line) =>
        line.map((value, i) => (i === this.activeColumn ? 'x' : value))
      );
      this.tablature = newTablature;
    },
    exportAsText() {
      const maxColumnWidth = 4; // Largeur fixe pour chaque colonne
      const text = this.tablature
        .map((line, index) => {
          const strings = ["E", "B", "G", "D", "A", "E"]; // Noms des cordes
          
          // Formate chaque ligne avec une largeur fixe par colonne
          const formattedLine = line
            .map((fret) => {
              // Ajuste chaque valeur pour qu'elle ait maxColumnWidth caractères (padding à droite avec des espaces)
              return fret.toString().padEnd(maxColumnWidth, ' ');
            })
            .join(''); // Joindre les colonnes

          return `${strings[index]}|${formattedLine}|`; // Formater chaque ligne
        })
        .join('\n'); // Joindre chaque ligne avec un saut de ligne

      // Créer le fichier texte et déclencher le téléchargement
      const blob = new Blob([text], { type: 'text/plain' });
      const link = document.createElement('a');
      link.href = URL.createObjectURL(blob);
      link.download = 'tablature.txt';
      link.click();
    }
  }
};
</script>

<style scoped>
#app {
  text-align: center;
  margin-top: 50px;
  font-family: "Darker Grotesque", sans-serif;
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

.add-space-button {
  background-color: #7acce4;
  color: black;
  border: none;
  padding: 10px 20px;
  margin: 10px;
  cursor: pointer;
}

.reset-all {
  background-color: #4caf50;
}

.reset-all:hover {
  background-color: #388e3c;
}
</style>
