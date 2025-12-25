<template>
  <div class="list">
    <div v-if="!expenses.length" class="empty">
      Aucune dépense pour le moment.
    </div>

    <ul v-else>
      <li v-for="e in expenses" :key="e.id" :class="rowClass(e)">
        <div class="left">
          <div class="desc">{{ e.description }}</div>
          <div class="meta">
            <span class="badge" :class="badgeClass(e)">
              {{ e.type === 'essentielle' ? 'Essentielle' : 'Non essentielle' }}
            </span>
            <span class="date">{{ formatDate(e.createdAt) }}</span>
          </div>
        </div>
        <div class="right">
          <span class="amount">{{ formatCurrency(e.amount) }}</span>
          <button class="delete" @click="$emit('delete', e.id)">✕</button>
        </div>
      </li>
    </ul>
  </div>
</template>

<script setup>
const props = defineProps({
  expenses: {
    type: Array,
    required: true
  }
})

function rowClass(e) {
  return e.type === 'essentielle' ? 'row essential' : 'row nonessential'
}
function badgeClass(e) {
  return e.type === 'essentielle' ? 'essential-badge' : 'nonessential-badge'
}

// Format XOF (Togo/WAEMU)
function formatCurrency(n) {
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
function formatDate(iso) {
  const d = new Date(iso)
  return d.toLocaleString('fr-FR', {
    day: '2-digit', month: '2-digit', year: 'numeric',
    hour: '2-digit', minute: '2-digit'
  })
}
</script>

<style scoped>
.list {
  background: #fff;
  border-radius: 14px;
  padding: 8px;
  box-shadow: 0 4px 16px rgba(0,0,0,0.06);
}
.empty {
  padding: 16px;
  color: #666;
  font-style: italic;
}
ul { list-style: none; padding: 0; margin: 0; }
.row {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 12px 10px;
  border-bottom: 1px dashed #ececec;
}
.row:last-child { border-bottom: none; }

.left { display: grid; gap: 6px; }
.desc { font-weight: 600; }
.meta { display: flex; gap: 8px; align-items: center; font-size: .85rem; color: #666; }

.badge {
  padding: 2px 8px;
  border-radius: 999px;
  font-weight: 600;
  font-size: .75rem;
  border: 1px solid transparent;
}

/* --- class binding (Essentielle vs Non essentielle) --- */
.essential { background: #faf9ff; }
.nonessential { background: #fff9f5; }
.essential-badge { background: #f3e8ff; border-color: #d8b4fe; }
.nonessential-badge { background: #fee2e2; border-color: #fecaca; }

.right { display: flex; align-items: center; gap: 10px; }
.amount { font-weight: 700; }
.delete {
  border: none; background: #fee2e2; color: #7f1d1d;
  padding: 6px 10px; border-radius: 8px; cursor: pointer;
}
.delete:hover { background: #fecaca; }
</style>
