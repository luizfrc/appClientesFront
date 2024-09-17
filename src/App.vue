<template>
  <div id="app">
    <h1>Cadastro de Clientes</h1>

    <!-- Formulário para adicionar/editar clientes -->
    <form @submit.prevent="isEditing ? updateClient() : createClient()">
      <input v-model="form.idUsuario" placeholder="ID Usuario" type="number" />
      <input v-model="form.Codigo" placeholder="Codigo" type="number" />
      <input v-model="form.Nome" placeholder="Nome" />
      <input v-model="form.CPF_CNPJ" placeholder="CPF ou CNPJ" type="number" />
      <input v-model="form.CEP" placeholder="CEP" type="number" @change="buscarCep" />
      <input v-model="form.Logradouro" placeholder="Logradouro" />
      <input v-model="form.Numero" placeholder="Numero" type="number" />
      <input v-model="form.Bairro" placeholder="Bairro" />
      <input v-model="form.Cidade" placeholder="Cidade" />
      <input v-model="form.UF" placeholder="UF" />
      <input v-model="form.Complemento" placeholder="Complemento" />
      <input v-model="form.Fone" placeholder="Telefone" type="number"/>
      <input v-model="form.LimiteCredito" placeholder="Limite Credito" type="number"/>
      <input v-model="form.Validade" placeholder="Validade" type="date"/>
      <button type="submit">{{ isEditing ? "Atualizar" : "Criar" }}</button>
    </form>

    <!-- Barra de pesquisa -->
    <input v-model="search" placeholder="Buscar..." />

    <!-- Tabela de clientes -->
    <table>
      <thead>
        <tr>
          <th>ID</th>
          <th>ID Usuario</th>
          <th>Codigo</th>
          <th>Nome</th>
          <th>CPF/CNPJ</th>
          <th>CEP</th>
          <th>Logradouro</th>
          <th>Numero</th>
          <th>Bairro</th>
          <th>Cidade</th>
          <th>UF</th>
          <th>Complemento</th>
          <th>Telefone</th>
          <th>Limite Credito</th>
          <th>Validade</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="cliente in filteredClients" :key="cliente.id">
          <td>{{ cliente.ID }}</td>
          <td>{{ cliente.idUsuario }}</td>
          <td>{{ cliente.Codigo }}</td>
          <td>{{ cliente.Nome }}</td>
          <td>{{ cliente.CPF_CNPJ }}</td>
          <td>{{ cliente.CEP }}</td>
          <td>{{ cliente.Logradouro }}</td>
          <td>{{ cliente.Numero }}</td>
          <td>{{ cliente.Bairro }}</td>
          <td>{{ cliente.Cidade }}</td>
          <td>{{ cliente.UF }}</td>
          <td>{{ cliente.Complemento }}</td>
          <td>{{ cliente.Fone }}</td>
          <td>{{ cliente.LimiteCredito }}</td>
          <td>{{ cliente.Validade }}</td>
          <td>
            <button @click="editClient(cliente)">Editar</button>
            <button @click="deleteClient(cliente.ID)">Excluir</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      clientes: [],
      form: {
        idUsuario: '',
        Codigo: '',
        Nome: '',
        CPF_CNPJ: '',
        CEP: '',
        Logradouro: '',
        Numero: '',
        Bairro: '',
        Cidade: '',
        UF: '',
        Complemento: '',
        Fone: '',
        LimiteCredito: '',
        Validade: ''
      },
      isEditing: false,
      editingId: null,
      search: ''
    };
  },
  computed: {
    filteredClients() {
      return this.clientes.filter(cliente => {
        return (
          cliente.Codigo.toLowerCase().includes(this.search.toLowerCase()) ||
          cliente.Nome.toLowerCase().includes(this.search.toLowerCase()) ||
          cliente.Cidade.toLowerCase().includes(this.search.toLowerCase()) ||
          cliente.CEP.toString().includes(this.search)
        );
      });
    }
  },
  methods: {
    fetchClients() {
      axios.get('http://localhost:3000/clientes').then(response => {
        this.clientes = response.data;
      });
    },
    createClient() {
      axios.post('http://localhost:3000/clientes', this.form).then(() => {
        this.fetchClients();
        this.resetForm();
      });
    },
    updateClient() {
      axios.put(`http://localhost:3000/clientes/${this.editingId}`, this.form).then(() => {
        this.fetchClients();
        this.resetForm();
      });
    },
    deleteClient(ID) {
      axios.delete(`http://localhost:3000/clientes/${ID}`).then(() => {
        this.fetchClients();
      });
    },
    editClient(cliente) {
      this.form = { ...cliente };
      this.isEditing = true;
      this.editingId = cliente.ID;
    },
    resetForm() {
      this.form = { 
        idUsuario: '',
        Codigo: '',
        Nome: '',
        CPF_CNPJ: '',
        CEP: '',
        Logradouro: '',
        Numero: '',
        Bairro: '',
        Cidade: '',
        UF: '',
        Complemento: '',
        Fone: '',
        LimiteCredito: '',
        Validade: ''
       };
      this.isEditing = false;
      this.editingId = null;
    },
    buscarCep() {
      if (this.form.CEP) {
        axios.get(`https://viacep.com.br/ws/${this.form.CEP}/json/`)
          .then(response => {
            const data = response.data;
            this.form.Logradouro = data.logradouro;
            this.form.Bairro = data.bairro;
            this.form.Cidade = data.localidade;
            this.form.UF = data.uf;
          })
          .catch(error => {
            console.error('Erro ao consultar CEP:', error);
          });
      }
    }
  },
  mounted() {
    this.fetchClients();
  }
};
</script>

<style scoped>
table {
  width: 100%;
  border-collapse: collapse;
  margin: 25px 15px 0px 0px;
}
th, td {
  padding: 10px;
  border: 1px solid #ccc;
}
input {
  margin: 5px 5px 5px 5px;
}
</style>
