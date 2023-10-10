<template>
  <div>
    <h1 class="white">Tirage au sort d'un élève</h1>
    <div>
      <label for="classe" class="white">Sélectionnez une classe :</label><br />
      <select v-model="classeSelectionnee" id="classe">
        <option value="">Toutes les classes</option>
        <option v-for="classe in classes" :key="classe">{{ classe }}</option>
      </select><br />
      <button @click="tirerAuSort" class="button-select">Tirer au sort</button><br /><br>
      <button @click="resetNotes" class="button-select">Réinitialiser les notes à 0</button><br />
    </div>
    <div v-if="eleveTire">
      <p>ID: {{ eleveTire.id }}</p>
      <p>Nom: {{ eleveTire.nomfamille }}</p>
      <p>Prénom: {{ eleveTire.prenom }}</p>
      <p>Classe : {{ eleveTire.classe }}</p>
      <p>Passage : {{eleveTire.passage}}</p>
      <label for="passage" class="white">Passage :</label>
      <input type="text" v-model="eleveTire.passage" id="passage">
      <button class="button-select" @click="sauvegarderPassage">Sauvegarder le passage</button>
      <p>Note : {{ eleveTire.note }}</p>
      <label for="note" class="white">Note :</label>
      <input type="number" v-model="eleveTire.note" id="note" max="20" min="0"/>
      <button @click="sauvegarderNote" class="button-select">Sauvegarder la note</button>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      eleves: [],
      eleveTire: null,
      classes: [],
      classeSelectionnee: ''
    };
  },
  methods: {
    chargerDonneesDepuisFichier() {
      // Essayez de charger les données depuis localStorage
      const elevesData = localStorage.getItem('eleves');
      if (elevesData) {
        this.eleves = JSON.parse(elevesData);
        this.classes = [...new Set(this.eleves.map(eleve => eleve.classe))];
      } else {
        // Si les données ne sont pas en localStorage, chargez-les depuis le fichier JSON initial
        axios.get('public/database.json')
            .then(response => {
              this.eleves = response.data.tables[0].data;
              this.classes = [...new Set(this.eleves.map(eleve => eleve.classe))];
            })
            .catch(error => {
              console.error('Erreur lors du chargement des données :', error);
            });
      }
    },
    tirerAuSort() {
      let elevesFiltres = this.eleves;

      if (this.classeSelectionnee !== '') {
        elevesFiltres = this.eleves.filter(eleve => eleve.classe === this.classeSelectionnee);
      }

      if (elevesFiltres.length > 0) {
        const randomIndex = Math.floor(Math.random() * elevesFiltres.length);
        this.eleveTire = elevesFiltres[randomIndex];
      }
    },
    sauvegarderNote() {
      // Mettez à jour localement la mise à jour de la note
      const eleveIndex = this.eleves.findIndex(eleve => eleve.id === this.eleveTire.id);
      if (eleveIndex !== -1) {
        this.eleves[eleveIndex].note = this.eleveTire.note;

        // Stockez les données localement dans localStorage
        localStorage.setItem('eleves', JSON.stringify(this.eleves));

        console.log('Note mise à jour localement.');
      } else {
        console.error('Impossible de trouver l\'élève dans le tableau.');
      }
    },
    sauvegarderPassage() {
      // Mettez à jour localement la mise à jour du passage
      const eleveIndex = this.eleves.findIndex(eleve => eleve.id === this.eleveTire.id);
      if (eleveIndex !== -1) {
        this.eleves[eleveIndex].passage = this.eleveTire.passage;

        // Stockez les données localement dans localStorage
        localStorage.setItem('eleves', JSON.stringify(this.eleves));

        console.log('Passage mis à jour localement.');
      } else {
        console.error('Impossible de trouver l\'élève dans le tableau.');
      }
    },
    resetNotes() {
      // Parcourez la liste des élèves et réinitialisez toutes les notes à 0
      this.eleves.forEach(eleve => {
        eleve.note = 0;
      });

      // Stockez les données localement dans localStorage
      localStorage.setItem('eleves', JSON.stringify(this.eleves));

      console.log('Toutes les notes ont été réinitialisées à 0.');
    }
  },
  mounted() {
    // Chargez les données depuis le stockage local ou le fichier JSON initial
    this.chargerDonneesDepuisFichier();
  }
};
</script>
