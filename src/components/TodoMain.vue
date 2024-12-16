<template>
  <div>
    <div class="flex flex-col gap-4 mb-4">
      <div class="flex justify-between items-center">
        <h1 class="text-white font-bold uppercase text-sm tracking-wider">Todo APP</h1>
        <div class="flex gap-2">
          <button
            @click="clearTasks"
            class="transition hover:scale-105 bg-primary text-white uppercase text-xs tracking-wider p-1 px-2 rounded-md min-w-12 hover:bg-primary/20 hover:border-primary border border-transparent"
          >
            Clear ALL
          </button>
          <button
            @click="clearCompleted"
            class="transition hover:scale-105 bg-primary text-white uppercase text-xs tracking-wider p-1 px-2 rounded-md min-w-12 hover:bg-primary/20 hover:border-primary border border-transparent"
          >
            Clear Completed
          </button>
        </div>
      </div>
      <div class="h-[1px] bg-primary"></div>
      <NewTodo @add-task="addTask" />
      <Transition>
        <span v-if="taskError" class="text-primary text-xs uppercase tracking-wide font-bold">
          {{ taskError }}</span
        >
      </Transition>
    </div>
    <TodoItem
      :tasks="tasks"
      @delete-task="deleteTask"
      @set-completed="setCompleted"
      @set-pending="setPending"
    />

    <div class="flex justify-between mt-4">
      <p class="text-white text-xs uppercase tracking-wide">Total Todos : {{ tasks.length }}</p>
      <p class="text-white text-xs uppercase tracking-wide">Completed : {{ getCompleted() }}</p>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import NewTodo from '@/components/NewTodo.vue'
import TodoItem from '@/components/TodoItem.vue'

const taskError = ref('')

const tasks = ref([])

const getCompleted = () => tasks.value.filter((task) => task.completed).length

const addTask = (task) => {
  const addId = tasks.value.length
  if (task != undefined) {
    tasks.value.push({ id: addId + 1, task: task, completed: false })
  } else {
    taskError.value = 'Cannot add empty task'
    setTimeout(() => {
      taskError.value = ''
    }, 2000)
  }
}

const setTodos = () => {
  localStorage.setItem('todos', JSON.stringify(tasks.value))
}

const getTodos = () => {
  tasks.value = JSON.parse(localStorage.getItem('todos')) ?? []
}

const setPending = (id) => {
  console.log(id)
  const index = tasks.value.findIndex((task) => task.id === id)
  tasks.value[index].completed = false
  setTodos()
}

const setCompleted = (id) => {
  console.log(id)
  const index = tasks.value.findIndex((task) => task.id === id)
  console.log(tasks.value[index])
  tasks.value[index].completed = true
  setTodos()
}

const deleteTask = (id) => {
  const index = tasks.value.findIndex((task) => task.id === id)
  tasks.value.splice(index, 1)
  setTodos()
}

const clearCompleted = () => {
  tasks.value = tasks.value.filter((task) => !task.completed)
  setTodos()
}

const clearTasks = () => {
  tasks.value = []
  setTodos()
}

onMounted(() => {
  getTodos()
})
</script>
