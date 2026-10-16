<script setup>
import { ref, computed } from 'vue'
const tarefas = ref([
  { id: 1, desc: 'Prova Geografia', status: 'pendente' },
  { id: 2, desc: 'Prova História', status: 'concluida' },
  { id: 3, desc: 'Trabalho DevWeb', status: 'pendente' }
]);

const novaTarefa = ref('')
const idAlterar = ref(null)
const filtro = ref('')

const tarefasPendentes = computed(() => {
  return tarefas.value.filter(t => t.status == 'pendente').length;
})

const tarefasConcluidas = computed(() => {
  return tarefas.value.filter(t => t.status == 'concluida').length;
})


const tarefasFiltradas = computed(() => {
  return tarefas.value.filter(t =>
    t.desc.toLowerCase().includes(filtro.value.trim().toLowerCase())
  );
});


function ordenar() {
  tarefas.value.sort((a, b) => a.desc.localeCompare(b.desc, 'pt-BR'))
}



function addTarefa() {
  if (!novaTarefa.value.trim()) return;

  if (idAlterar.value === null) {
    let maiorID = tarefas.value.length
      ? Math.max(...tarefas.value.map(item => item.id))
      : 0;

    tarefas.value.push({
      id: maiorID + 1,
      desc: novaTarefa.value.trim(),
      status: 'pendente'
    });
  } else {
    const tarefa = tarefas.value.find(t => t.id === idAlterar.value);

    if (tarefa) {
      tarefa.desc = novaTarefa.value.trim();
    }

    idAlterar.value = null;
  }

  novaTarefa.value = '';
}



function deleteTarefa(item) {
  const posicao = tarefas.value.findIndex(t => t.id === item.id);

  if (posicao !== -1) {
    tarefas.value.splice(posicao, 1);
  }
}



function editTarefa(item) {
  idAlterar.value = item.id;
  novaTarefa.value = item.desc;
}


function marcarConcluida(id) {
  const posicao = tarefas.value.findIndex(t => t.id === id);

  if (posicao === -1) return;

  if (tarefas.value[posicao].status === 'concluida') {
    tarefas.value[posicao].status = 'pendente';
  } else {
    tarefas.value[posicao].status = 'concluida';
  }
}

</script>

<template>
  <div class="container">
    <input type="text" v-model="novaTarefa">
    <button @click="addTarefa">Adicionar</button>
    <ul>
      <li v-for="item in tarefasFiltradas" :key="item.id">
        <span
          @click="marcarConcluida(item.id)"
          :class="{ concluida: item.status == 'concluida'}"
        >
            {{ item.desc }}
        </span>
        <span>
          <a href="#" @click.prevent="deleteTarefa(item)">Delete</a>
          <a href="#" @click.prevent="editTarefa(item)">Edit</a>
        </span>
      </li>
    </ul>
    <div>
      <input type="text" placeholder="Filtrar Tarefa..." v-model="filtro">
      <button @click.prevent="ordenar()">Ordenar</button>
    </div>
    <div>
      <span>Pendentes: {{ tarefasPendentes }}</span>
      <span>Concluídas: {{ tarefasConcluidas }}</span>
    </div>
  </div>
</template>

<style scoped>
li {
  cursor: pointer;
}

.concluida {
  text-decoration: line-through;
}
</style>
