<template>
    <div>
      
      <h1>Gerenciar Vocábulos</h1>
  
      <!-- Formulário para cadastro -->
      <form @submit.prevent="cadastrarVocabulo">
        <div>
          <label for="termo">Termo:</label>
          <input id="termo" v-model="novoVocabulo.termo" required />
        </div>
        <div>
          <label for="significado">Significado:</label>
          <input id="significado" v-model="novoVocabulo.significado" required />
        </div>
        <div>
          <label for="versao">Versão:</label>
          <input id="versao" type="number" v-model.number="novoVocabulo.versao" required />
        </div>
        <div>
          <label for="dataHoraDesativacao">Data de Desativação:</label>
          <input id="dataHoraDesativacao" type="datetime-local" v-model="novoVocabulo.dataHoraDesativacao" />
        </div>
        <button type="submit">Cadastrar</button>
      </form>
  
      <!-- Busca -->
      <form @submit.prevent="buscarVocabulos">
        <h2>Buscar Vocábulos</h2>
        <div>
          <label for="termoBusca">Termo:</label>
          <input id="termoBusca" v-model="busca.termo" />
        </div>
        <div>
          <label for="versaoBusca">Versão:</label>
          <input id="versaoBusca" type="number" v-model.number="busca.versao" />
        </div>
        <button type="submit">Buscar</button>
      </form>
  
      <!-- Lista de vocábulos -->
      <h2>Lista de Vocábulos</h2>
      <p v-if="vocabulos.length === 0">Nenhum vocábulo foi encontrado.</p>
      <table v-else>
        <thead>
          <tr>
            <th>ID</th>
            <th>Termo</th>
            <th>Significado</th>
            <th>Versão</th>
            <th>Situação</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="vocabulo in vocabulos" :key="vocabulo.id">
            <td>{{ vocabulo.id }}</td>
            <td>{{ vocabulo.termo }}</td>
            <td>{{ vocabulo.significado }}</td>
            <td>{{ vocabulo.versao }}</td>
            <td>{{ vocabulo.dataHoraDesativacao ? 'Desativado' : 'Ativo' }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue';
  import axios from 'axios';
  
  // Estado
  const vocabulos = ref([]);
  const novoVocabulo = ref({
    termo: '',
    significado: '',
    versao: null,
    dataHoraDesativacao: null,
  });
  const busca = ref({
    termo: '',
    versao: null,
  });
  
  // URL base do backend
  const baseUrl = '/vocabulo';
  
  // Funções
  const carregarVocabulos = async () => {
    try {
      const response = await axios.get(baseUrl);
      vocabulos.value = response.data;
    } catch (error) {
      console.error('Erro ao carregar vocábulos:', error);
    }
  };
  
  const cadastrarVocabulo = async () => {
    try {
      await axios.post(baseUrl, novoVocabulo.value);
      alert('Vocábulo cadastrado com sucesso!');
      carregarVocabulos();
      // Limpar formulário
      novoVocabulo.value = {
        termo: '',
        significado: '',
        versao: null,
        dataHoraDesativacao: null,
      };
    } catch (error) {
      console.error('Erro ao cadastrar vocábulo:', error);
      alert('Erro ao cadastrar vocábulo. Verifique os dados.');
    }
  };
  
  const buscarVocabulos = async () => {
    try {
      const { termo, versao } = busca.value;
      const response = await axios.get(`${baseUrl}/${termo}/${versao}`);
      vocabulos.value = response.data;
      if (vocabulos.value.length === 0) {
        alert('Nenhum vocábulo foi encontrado para os critérios fornecidos.');
      }
    } catch (error) {
      console.error('Erro ao buscar vocábulos:', error);
    }
  };
  
  const irParaVocabulos = () => {
    carregarVocabulos();
  };
  
  // Inicialização
  onMounted(() => {
    carregarVocabulos();
  });
  </script>
  
  <style>
  /* Estilização simples para organização */
  nav ul {
    display: flex;
    list-style: none;
    padding: 0;
  }
  
  nav ul li {
    margin-right: 15px;
  }
  
  table {
    border-collapse: collapse;
    width: 100%;
    margin-top: 20px;
  }
  
  table, th, td {
    border: 1px solid black;
  }
  
  th, td {
    padding: 10px;
    text-align: left;
  }
  </style>
  