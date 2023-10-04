<template>
  <div>
    <h1>Tirage au sort d'un élève</h1>
    <div>
      <label for="classe">Sélectionnez une classe :</label>
      <select v-model="classeSelectionnee" id="classe">
        <option value="">Toutes les classes</option>
        <option v-for="classe in classes" :key="classe">{{ classe }}</option>
      </select>
      <button @click="tirerAuSort">Tirer au sort</button>
    </div>
    <div v-if="eleveTire">
      <p>ID: {{ eleveTire.id }}</p>
      <p>Nom: {{ eleveTire.nomfamille }}</p>
      <p>Prénom: {{ eleveTire.prenom }}</p>
      <p>Passage: {{ eleveTire.passage }}</p>
      <p>Note: {{ eleveTire.note }}</p>
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
      classes: [], // Tableau des classes disponibles
      classeSelectionnee: '' // Classe sélectionnée dans le menu dépliant
    };
  },
  methods: {
    tirerAuSort() {
      let elevesFiltres = this.eleves;

      if (this.classeSelectionnee !== '') {
        // Filtrer les élèves par classe si une classe est sélectionnée
        elevesFiltres = this.eleves.filter(eleve => eleve.classe === this.classeSelectionnee);
      }

      if (elevesFiltres.length > 0) {
        const randomIndex = Math.floor(Math.random() * elevesFiltres.length);
        this.eleveTire = elevesFiltres[randomIndex];
      }
    }
  },
  mounted() {
    axios.get('/database.json')
        .then(response => {
          this.eleves = response.data.tables[0].data;

          // Extraire les classes uniques du JSON
          this.classes = [...new Set(this.eleves.map(eleve => eleve.classe))];
        })
        .catch(error => {
          console.error('Erreur lors du chargement des données :', error);
        });
  }
};
</script>
