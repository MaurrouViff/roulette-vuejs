<template>
  <div>
    <h1>Tirage au sort d'un élève</h1>
    <button @click="tirerAuSort">Tirer au sort</button>
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
      eleveTire: null
    };
  },
  methods: {
    tirerAuSort() {
      if (this.eleves.length > 0) {
        const randomIndex = Math.floor(Math.random() * this.eleves.length);
        this.eleveTire = this.eleves[randomIndex];
      }
    }
  },
  mounted() {
    axios.get('/database.json')
        .then(response => {
          this.eleves = response.data.tables[0].data;
        })
        .catch(error => {
          console.error('Erreur lors du chargement des données :', error);
        });
  }
};
</script>
