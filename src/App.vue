<script setup>
import { ref, onMounted, computed, watch } from 'vue'
const todos = ref([])
const input_content = ref('')
watch(todos, (newVal) => {
	localStorage.setItem('todos', JSON.stringify(newVal))
}, {
	deep: true
})
const addTodo = () => {
	if (input_content.value.trim() === '' ) {
		return
	}
	todos.value.push({
		content: input_content.value,
		done: false,
		editable: false,
	})
}
const removeTodo = (todo) => {
	todos.value = todos.value.filter((t) => t !== todo)
}
onMounted(() => {
	todos.value = JSON.parse(localStorage.getItem('todos')) || []
})

const filter = ref('all')

const filtered_todos = computed(() => {
  if (filter.value === 'uncompleted') {
    return todos.value.filter(todo => !todo.done)
  } else {
    return todos.value
  }
})
</script>

<template>
	<main class="app">
		<h1>ToDo Mufti App</h1>
		<section class="create-todo">
			<h3>BUAT KEGIATAN</h3>

			<form id="new-todo-form" @submit.prevent="addTodo">
				<h4>Kegiatan Apa yang Ingin Anda Lakukan?</h4>
				<input 
					type="text" 
					name="content" 
					id="content" 
					placeholder="contoh: exploring genshin"
					v-model="input_content" />
				<input type="submit" value="Tambah Kegiatan" />
			</form>
		</section>

		<section class="todo-list">
			<h3>LIST KEGIATAN</h3>
			
      <div class="filters">
        <label>
          <button :class="{active: filter === 'all'}" @click="filter = 'all'">Semua</button>
        </label>
        <label>
          <button :class="{active: filter === 'uncompleted'}" @click="filter = 'uncompleted'">Belum Selesai</button>
        </label>
      </div>
      <div class="list" id="todo-list">
        <div v-for="todo in filtered_todos" :class="`todo-item ${todo.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="todo.done" />
					</label>

					<div class="todo-content">
						<input type="text" v-model="todo.content" />
					</div>

					<div class="actions">
						<button class="delete" @click="removeTodo(todo)">Hapus</button>
					</div>
				</div>
      </div>
	</section>

	</main>
</template>