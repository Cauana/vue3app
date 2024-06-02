<template>
    <div>
      <h1>Gerencie Vocábulos</h1>
      <form @submit.prevent="addVocabulo">
        <input v-model="termo" placeholder="Termo" required>
        <input v-model="significado" placeholder="Significado" required>
        <input v-model="versao" placeholder="Versão" required>
        <input type="datetime-local" v-model="dataHoraDesativacao">
        <button type="submit">Adicionar Vocábulo</button>
      </form>
  
      <div>
        <input v-model="searchTermo" placeholder="Buscar por termo">
        <input v-model="searchVersao" placeholder="Buscar por versão">
        <button @click="searchVocabulo">Buscar</button>
      </div>
  
      <ul>
        <li v-for="vocabulo in vocabulos" :key="vocabulo.id">
          {{ vocabulo.id }} - {{ vocabulo.termo }} - {{ vocabulo.significado }} - {{ vocabulo.versao }} - 
          {{ vocabulo.dataHoraDesativacao ? 'Não' : 'Sim' }}
        </li>
      </ul>
  
      <div v-if="vocabulos.length === 0">
        Nenhum vocábulo foi encontrado para os critérios fornecidos
      </div>
    </div>
  </template>
  
  <script>
  import axios from 'axios';
  
  export default {
    data() {
      return {
        vocabulos: [],
        termo: '',
        significado: '',
        versao: '',
        dataHoraDesativacao: null,
        searchTermo: '',
        searchVersao: ''
      };
    },
    methods: {
      async fetchVocabulos() {
        try {
          const response = await axios.get('/vocabulo');
          this.vocabulos = response.data;
        } catch (error) {
          console.error(error);
        }
      },
      async addVocabulo() {
        try {
          const newVocabulo = {
            termo: this.termo,
            significado: this.significado,
            versao: this.versao,
            dataHoraDesativacao: this.dataHoraDesativacao
          };
          await axios.post('/vocabulo', newVocabulo);
          this.fetchVocabulos();
          this.termo = '';
          this.significado = '';
          this.versao = '';
          this.dataHoraDesativacao = null;
        } catch (error) {
          console.error(error);
        }
      },
      async searchVocabulo() {
        try {
          const response = await axios.get(`/vocabulo/${this.searchTermo}/${this.searchVersao}`);
          this.vocabulos = response.data;
        } catch (error) {
          console.error(error);
        }
      }
    },
    mounted() {
      this.fetchVocabulos();
    }
  };
  </script>
  