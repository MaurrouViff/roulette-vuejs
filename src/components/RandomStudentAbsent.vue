<template>
  <div>
    <h1>Tirage au sort des élèves absent</h1>
    <div>
      <label for="classe">Sélectionnez une classe : </label><br />
      <select v-model="classeSelectionnee"><br>
        <option value="">Toutes les classes</option>
        <option v-for="classe in classes" :key="classe">{{ classe }}</option>
      </select><br>
      <button @click="tirerAuSort" class="button-select">Tirer au sort</button>
    </div>
    <div v-if="eleveTire">
      <p>ID: {{ eleveTire.id }}</p>
      <p>Nom: {{ eleveTire.nomfamille }}</p>
      <p>Prénom: {{ eleveTire.prenom }}</p>
      <p>Classe : {{eleveTire.classe}}</p>
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
      classeSelectionnee: '', // Classe sélectionnée dans le menu dépliant
      elevesFiltres: [] // Élèves filtrés par "passage"
    };
  },
  methods: {
    tirerAuSort() {
      if (this.elevesFiltres.length > 0) {
        const randomIndex = Math.floor(Math.random() * this.elevesFiltres.length);
        this.eleveTire = this.elevesFiltres[randomIndex];
      }
    },
    filtrerEleves() {
      if (this.classeSelectionnee === '') {
        this.elevesFiltres = this.eleves.filter(eleve => eleve.passage === 'absent');
      } else {
        this.elevesFiltres = this.eleves.filter(
            eleve => eleve.classe === this.classeSelectionnee && eleve.passage === 'absent'
        );
      }
    }
  },
  mounted() {
    axios.get('public/database.json')
        .then(response => {
          this.eleves = response.data.tables[0].data;

          // Extraire les classes uniques du JSON
          this.classes = [...new Set(this.eleves.map(eleve => eleve.classe))];

          // Filtrer les élèves par "passage" lors du chargement initial
          this.filtrerEleves();
        })
        .catch(error => {
          console.error('Erreur lors du chargement des données :', error);
        });
  },
  watch: {
    classeSelectionnee: 'filtrerEleves' // Réexécute la fonction de filtrage lorsque la classe sélectionnée change
  }
};
</script>
