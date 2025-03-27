<script setup lang="ts">
import axios from 'axios'
import { ref } from 'vue'
import 'bootstrap/dist/css/bootstrap.min.css'
import 'bootstrap/dist/js/bootstrap.min.js'
const email = ref('')
const apiResponse = ref<Record<string, any> | null>(null)
const loading = ref(false)
const errorMessage = ref<string | null>(null)

async function validar() {
  if (!email.value) return

  loading.value = true
  errorMessage.value = null

  try {
    const { data } = await axios.get(`https://disify.com/api/email/${email.value}`)
    apiResponse.value = data
  } catch (error) {
    errorMessage.value = 'Erro ao validar o e-mail. Tente novamente.'
    apiResponse.value = null
  } finally {
    loading.value = false
  }
}
</script>

<template>
  <div class="container">
    <h2>Valide e verifique um endereço de e-mail.</h2>
    <input type="text" class="form-control" v-model="email" placeholder="Digite seu e-mail" />
    <button @click="validar" :disabled="!email || loading">
      {{ loading ? 'Validando...' : 'Validar' }}
    </button>
    <p v-if="errorMessage" class="error">{{ errorMessage }}</p>

    <table v-if="apiResponse" class="table table-striped">
      <thead>
        <tr>
          <th>E-mail Consultado</th>
          <th>Formato Válido</th>
          <th>Domínio</th>
          <th>DNS Válido</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>{{ email }}</td>
          <td>{{ apiResponse.format ? '✔️' : '❌' }}</td>
          <td>{{ apiResponse.domain || '—' }}</td>
          <td>{{ apiResponse.dns ? '✔️' : '❌' }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<style scoped>
.container {
  max-width: 400px;
  margin: auto;
  text-align: center;
}

input {
  width: 100%;
  padding: 8px;
  margin-bottom: 10px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

button {
  padding: 8px 12px;
  border: none;
  background-color: #007bff;
  color: white;
  cursor: pointer;
  border-radius: 4px;
}

button:disabled {
  background-color: #aaa;
  cursor: not-allowed;
}

.error {
  color: red;
  margin-top: 10px;
}
</style>
