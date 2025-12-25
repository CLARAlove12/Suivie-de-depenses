<template>
  <main class="container">
    <h1>Suivi de dépenses</h1>

    <section class="top">
      <DepensesForm @add-expense="addExpense" />

      <div class="summary">
        <h2>Résumé</h2>
        <div class="cards">
          <div class="card">
            <div class="label">Total</div>
            <div class="value">{{ money(total) }}</div>
          </div>
          <div class="card">
            <div class="label">Essentielles</div>
            <div class="value">{{ money(essentialTotal) }}</div>
          </div>
          <div class="card">
            <div class="label">Non essentielles</div>
            <div class="value">{{ money(nonEssentialTotal) }}</div>
          </div>
        </div>

        <div class="filters">
          <button
            :class="{'active': filter==='all'}"
            @click="filter='all'">Toutes</button>
          <button
            :class="{'active': filter==='essentielle'}"
            @click="filter='essentielle'">Essentielles</button>
          <button
            :class="{'active': filter==='non_essentielle'}"
            @click="filter='non_essentielle'">Non essentielles</button>
        </div>
      </div>
    </section>

    <section>
      <DepensesList
        :expenses="filteredExpenses"
        @delete="deleteExpense"
      />
    </section>
  </main>
</template>

<script setup>
import { ref, computed, onMounted, watch } from 'vue'
import DepensesForm from './components/depensesForm.vue'
import DepensesList from './components/depensesList.vue'


const expenses = ref([])
/*
  Structure d'un élément:
  {
    id: string,
    description: string,
    amount: number,
    type: 'essentielle' | 'non_essentielle',
    createdAt: string (ISO)
  }
*/

const filter = ref('all') 


const total = computed(() =>
  expenses.value.reduce((sum, e) => sum + e.amount, 0)
)
const essentialTotal = computed(() =>
  expenses.value
    .filter(e => e.type === 'essentielle')
    .reduce((sum, e) => sum + e.amount, 0)
)
const nonEssentialTotal = computed(() =>
  expenses.value
    .filter(e => e.type === 'non_essentielle')
    .reduce((sum, e) => sum + e.amount, 0)
)


const filteredExpenses = computed(() => {
  if (filter.value === 'all') return expenses.value
  return expenses.value.filter(e => e.type === filter.value)
})


function addExpense(expense) {
  expenses.value = [expense, ...expenses.value]
}
function deleteExpense(id) {
  expenses.value = expenses.value.filter(e => e.id !== id)
}


function money(n) {
  try {
    return new Intl.NumberFormat('fr-FR', {
      style: 'currency',
      currency: 'XOF',
      maximumFractionDigits: 0
    }).format(n)
  } catch {
    return `${n} XOF`
  }
}


const LS_KEY = 'vue-expenses-v1'
onMounted(() => {
  const raw = localStorage.getItem(LS_KEY)
  if (raw) {
    try { expenses.value = JSON.parse(raw) } catch {}
  }
})
watch(expenses, (val) => {
  localStorage.setItem(LS_KEY, JSON.stringify(val))
}, { deep: true })
</script>

<style scoped>
.container {
  max-width: 980px;
  margin: 24px auto;
  padding: 0 16px 24px;
  display: grid;
  gap: 20px;
}

h1 { 
  margin: 8px 0 0;
  font-size: 2rem;
}

.top {
  display: grid;
  gap: 16px;
  grid-template-columns: 1fr 1fr;
}

.summary {
  background: #fff;
  padding: 16px;
  border-radius: 14px;
  box-shadow: 0 4px 16px rgba(0,0,0,0.06);
  display: grid;
  gap: 12px;
}

.summary h2 {
  margin: 0;
  font-size: 1.25rem;
}

.cards {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 8px;
}

.card {
  background: #fafafa;
  border-radius: 12px;
  padding: 12px;
}

.label { 
  color: #666; 
  font-size: .9rem;
  margin-bottom: 4px;
}

.value { 
  font-size: 1.25rem; 
  font-weight: 800;
  word-break: break-word;
}

.filters { 
  display: flex; 
  gap: 8px; 
  margin-top: 6px;
  flex-wrap: wrap;
}

.filters button {
  padding: 8px 10px; 
  border-radius: 10px; 
  border: 1px solid #ddd;
  background: #fff; 
  cursor: pointer; 
  font-weight: 600;
  transition: all 0.2s;
  flex: 1;
  min-width: fit-content;
}

.filters button.active { 
  background: #ede9fe; 
  border-color: #c4b5fd; 
}

.filters button:hover {
  border-color: #c4b5fd;
}

/* === RESPONSIVE === */

/* Tablettes et écrans moyens */
@media (max-width: 900px) {
  .top { 
    grid-template-columns: 1fr; 
  }
}

/* Tablettes en portrait */
@media (max-width: 768px) {
  .container {
    margin: 16px auto;
    padding: 0 12px 16px;
    gap: 16px;
  }

  h1 {
    font-size: 1.75rem;
  }

  .summary {
    padding: 14px;
  }

  .summary h2 {
    font-size: 1.15rem;
  }

  .cards {
    gap: 6px;
  }

  .card {
    padding: 10px;
  }

  .label {
    font-size: .85rem;
  }

  .value {
    font-size: 1.15rem;
  }

  .filters button {
    padding: 7px 9px;
    font-size: .9rem;
  }
}

/* Mobile */
@media (max-width: 480px) {
  .container {
    margin: 12px auto;
    padding: 0 10px 12px;
    gap: 14px;
  }

  h1 {
    font-size: 1.5rem;
    margin: 4px 0 0;
  }

  .summary {
    padding: 12px;
    gap: 10px;
  }

  .summary h2 {
    font-size: 1rem;
  }

  .cards {
    grid-template-columns: 1fr;
    gap: 8px;
  }

  .card {
    padding: 12px;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  .label {
    font-size: .9rem;
    margin-bottom: 0;
  }

  .value {
    font-size: 1.3rem;
  }

  .filters {
    flex-direction: column;
    gap: 6px;
  }

  .filters button {
    padding: 10px;
    font-size: .95rem;
    flex: none;
    width: 100%;
  }
}

/* Très petits écrans */
@media (max-width: 360px) {
  .container {
    padding: 0 8px 12px;
  }

  h1 {
    font-size: 1.35rem;
  }

  .summary {
    padding: 10px;
  }

  .card {
    padding: 10px;
  }

  .value {
    font-size: 1.2rem;
  }
}
</style>