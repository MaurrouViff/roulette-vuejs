<template>
  <div>
    <h1 class="white">Tirage au sort d'un élève</h1>
    <div>
      <label for="classe" class="white">Sélectionnez une classe :</label><br />
      <select v-model="classeSelectionnee" id="classe">
        <option value="">Toutes les classes</option>
        <option v-for="classe in classes" :key="classe">{{ classe }}</option>
      </select><br />
      <button @click="tirerAuSort" class="button-select">Tirer au sort</button>
    </div>
    <div v-if="eleveTire">
      <p>ID: {{ eleveTire.id }}</p>
      <p>Nom: {{ eleveTire.nomfamille }}</p>
      <p>Prénom: {{ eleveTire.prenom }}</p>
      <p>Classe : {{ eleveTire.classe }}</p>
      <p>Passage: {{ eleveTire.passage }}</p>
      <p>Note : {{eleveTire.note}}</p>
      <label for="note" class="white">Note :</label>
      <input type="number" v-model="eleveTire.note" id="note" />
      <button @click="sauvegarderNote" class="button-select">Sauvegarder la note</button>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

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
      // La note est déjà mise à jour dans eleveTire grâce à v-model

      // Mettez à jour localement la mise à jour de la note
      const eleveIndex = this.eleves.findIndex(eleve => eleve.id === this.eleveTire.id);
      if (eleveIndex !== -1) {
        this.eleves[eleveIndex].note = this.eleveTire.note;
        localStorage.setItem('eleves', JSON.stringify(this.eleves));
      } else {
        console.error('Impossible de trouver l\'élève dans le tableau.');
      }
    }
  },
  mounted() {
    // Chargez les données depuis le stockage local s'il y en a
    const elevesData = localStorage.getItem('eleves');
    if (elevesData) {
      this.eleves = JSON.parse(elevesData);
    } else {
      axios.get('/database.json')
          .then(response => {
            this.eleves = response.data.tables[0].data;
            this.classes = [...new Set(this.eleves.map(eleve => eleve.classe))];
          })
          .catch(error => {
            console.error('Erreur lors du chargement des données :', error);
          });
    }
  }
};
</script>
