<template>
  <div id="app">
    <div class="usefull">
      <h1>Générateur de Tablature 🎸</h1>
      <div>
        <h2>Comment utiliser le générateur de tablature ?</h2>
        <ul>
          <li>Pour sélectionner une note cliquée simplement sur la case de la corde correspondante.</li>
          <li>Pour les slide, bend, pull-off et hammer-on, sélectionnez d'abord le bouton puis cliquer sur les deux
            notes voulues dans l'ordre.<br> Par exemple pour écrire 1h3, appuyez sur le bouton hammer-on, ensuite la
            case 1 et
            la case 3 de la corde souhaitée.</li>
          <li>En cas d'erreur, vous pouvez revenir en arrière à la manière d'un Ctrl+Z avec le bouton "Annuler".</li>
          <li>Composez puis exporter avec le bouton "Exporter en .txt" ou "Exporter en .pdf".</li>
        </ul>
      </div>



      <GuitarFretboard @fret-selected="updateTablature" @mute-column="muteActiveColumn" :frets="24" />

      <Tablature :tablature="tablature" :activeColumn="activeColumn" @column-selected="setActiveColumn" />

      <button @click="resetActiveColumn" class="reset-button">
        Effacer la colonne sélectionnée
      </button>

      <button @click="resetAllColumns" class="reset-button reset-all">
        Effacer toute la tablature
      </button>

      <button @click="addSpace" class="add-space-button">Ajouter un espace</button>

      <button @click="undo" class="undo-button" :disabled="history.length === 0"> Annuler ↻</button>

      <button @click="exportAsText" class="export-button">Exporter en .txt</button>
      <button @click="exportAsPDF" class="export-button">Exporter en .pdf</button>
    </div>
  </div>
</template>

<script>
import GuitarFretboard from './components/GuitarFretboard.vue';
import Tablature from './components/Tablature.vue';
import jsPDF from 'jspdf';

export default {
  components: {
    GuitarFretboard,
    Tablature
  },
  data() {
    return {
      tablature: Array(6).fill(Array(16).fill('-')),
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
      this.tablature = Array(6).fill(Array(16).fill('-'));
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
      const maxColumnWidth = 5; // largeur de collone (caracteres)

      const text = this.tablature
        .map((line, index) => {
          const strings = ["E", "B", "G", "D", "A", "E"];

          const formattedLine = line
            .map((fret) => {
              // ajuster maxColumnWidth caractères (padding à droite avec des espaces)
              return fret.toString().padEnd(maxColumnWidth, ' ');
            })
            .join('');

          return `${strings[index]}|${formattedLine}|`;
        })
        .join('\n'); // saute ligne

      // créer le fichier texte et dl
      const blob = new Blob([text], { type: 'text/plain' });
      const link = document.createElement('a');
      link.href = URL.createObjectURL(blob);
      link.download = 'tablature.txt';
      link.click();
    },
    exportAsPDF() {
      const doc = new jsPDF();

      doc.setFont('Courier', 'normal');
      doc.setFontSize(9);

      const lineHeight = 6;
      const maxColumnWidth = 5; // Largeur maximale pour chaque colonne (5 caractères pour "20b20" par exemple)
      const columnsPerPage = 16; // nombre de collone par page
      const marginX = 10; // 
      let y = 10;

      for (let startCol = 0; startCol < this.tablature[0].length; startCol += columnsPerPage) {
        for (let stringIndex = 0; stringIndex < this.tablature.length; stringIndex++) {
          const stringName = ['E', 'B', 'G', 'D', 'A', 'E'][stringIndex];

          const segment = this.tablature[stringIndex].slice(startCol, startCol + columnsPerPage);

          const line = `${stringName}| ${segment.map(fret => fret.toString().padEnd(maxColumnWidth, ' ')).join(' ')} |`;

          doc.text(line, marginX, y);
          y += lineHeight;
        }

        // espace vertical chaque tab du pdf
        y += 2;
      }
      doc.save('tablature.pdf');
    },
  },
};
</script>

<style scoped>
#app {
  display: flex;
  justify-content: center;
  align-items: center;
  padding-top: 50px;
  padding-bottom: 50%;
  text-align: center;
  font-family: "Poppins", sans-serif;
  font-weight: 400;
  background-image: url("assets/background2.jpg");
  background-size: cover;
  background-position: center;
  box-sizing: border-box;
  

}

.usefull {
  background-color: rgba(255, 255, 255, 0.8);
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
  width: 70vw; 
  height: auto; 
}

h1 {
  font-family: "Darker Grotesque", sans-serif;
}

h2 {
  font-size: 20px;
  font-weight: 500;
  margin-bottom: 10px;
}


.reset-button {
  background-color: #f44336;
  color: white;
  border: none;
  padding: 10px 20px;
  margin: 10px;
  cursor: pointer;
  font-family: "Poppins", sans-serif;
  border-radius: 5px;
}

.reset-button:hover {
  background-color: #d32f2f;
}

.add-space-button {
  background-color: #277fc2;
  color: rgb(255, 255, 255);
  border: none;
  padding: 10px 20px;
  margin: 10px;
  cursor: pointer;
  font-family: "Poppins", sans-serif;
  border-radius: 5px
}

.add-space-button:hover {
  background-color: #1b5e91;
}

.reset-all {
  background-color: #f44336;
  font-family: "Poppins", sans-serif;
}

.reset-all:hover {
  background-color: #d32f2f;
}

.undo-button {
  background-color: #277fc2;
  color: rgb(255, 255, 255);
  border: none;
  padding: 10px 20px;
  margin: 10px;
  cursor: pointer;
  font-family: "Poppins", sans-serif;
  border-radius: 5px
}

.undo-button:hover {
  background-color: #1b5e91
}

.export-button {
  background-color: #1ba018;
  color: rgb(255, 255, 255);
  border: none;
  padding: 10px 20px;
  margin: 10px;
  cursor: pointer;
  font-family: "Poppins", sans-serif;
  border-radius: 5px
}

.export-button:hover {
  background-color: #0d8c1b;
}

ul {
  text-align: left;
}
</style>
