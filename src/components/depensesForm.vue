<template>
  <form @submit.prevent="submit">
    <div class="row">
      <label>Description</label>
      <input
        v-model.trim="description"
        type="text"
        placeholder="Ex: Transport, Riz, Netflix..."
      />
    </div>

    <div class="row">
      <label>Montant</label>
      <input
        v-model.number="amount"
        type="number"
        min="0"
        step="100"
        placeholder="Ex: 1500"
      />
    </div>

    <div class="row">
      <label>Type</label>
      <select v-model="type">
        <option value="essentielle">Essentielle</option>
        <option value="non_essentielle">Non essentielle</option>
      </select>
    </div>

    <p v-if="error" class="error">{{ error }}</p>

    <button type="submit">Ajouter</button>
  </form>
</template>

<script setup>
import { ref } from 'vue'

const description = ref('')
const amount = ref(null)
const type = ref('essentielle')
const error = ref('')

const emit = defineEmits(['add-expense'])

function reset() {
  description.value = ''
  amount.value = null
  type.value = 'essentielle'
  error.value = ''
}

function submit() {
  if (!description.value) {
    error.value = 'La description est obligatoire.'
    return
  }
  if (!amount.value || amount.value <= 0) {
    error.value = 'Le montant doit être supérieur à 0.'
    return
  }

  const expense = {
    id: Date.now().toString(),
    description: description.value,
    amount: Number(amount.value),
    type: type.value, // 'essentielle' OU 'non_essentielle'
    createdAt: new Date().toISOString()
  }

  emit('add-expense', expense)
  reset()
}
</script>

<style scoped>
form {
  display: grid;
  gap: 12px;
  background: #fff;
  padding: 16px;
  border-radius: 14px;
  box-shadow: 0 4px 16px rgba(0,0,0,0.06);
}
.row {
  display: grid;
  gap: 6px;
}
label { font-weight: 600; }
input, select {
  padding: 8px 10px;
  border: 1px solid #ddd;
  border-radius: 10px;
  outline: none;
}
input:focus, select:focus { border-color: #7c3aed; }
button {
  padding: 10px 14px;
  border: none;
  border-radius: 12px;
  cursor: pointer;
  font-weight: 600;
  background: #C8A27A;
  color: white;
}
.error { color: #b00020; font-size: 0.9rem; }
</style>
