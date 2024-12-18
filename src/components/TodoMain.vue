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
      <Transition
        enter-active-class="transition-transform transition-opacity duration-200 ease-out"
        enter-from-class="transform translate-y-full opacity-0"
        enter-to-class="transform translate-y-0 opacity-100"
        leave-active-class="transition-transform transition-opacity duration-200 ease-out"
        leave-from-class="opacity-100"
        leave-to-class="opacity-0"
      >
        <span v-if="taskError" class="text-primary text-xs uppercase tracking-wide font-bold">
          {{ taskError }}
        </span>
      </Transition>
    </div>
    <TodoItem
      :tasks="tasks"
      @delete-task="deleteTask"
      @set-completed="setCompleted"
      @set-pending="setPending"
      @edit-task="editTask"
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

//error text when trying to add a new task with empty string
const taskError = ref(null)

//array containing all the tasks
const tasks = ref([])

//Get the completed tasks count
const getCompleted = () => tasks.value.filter((task) => task.completed).length

//Add a new task into the tasks array
const addTask = (task) => {
  const addId = tasks.value.length
  if (typeof task === 'undefined' || task.toString().trim() === '') {
    taskError.value = 'Cannot add empty task'
    setTimeout(() => {
      taskError.value = null
    }, 2000)
    return
  }
  tasks.value.push({ id: addId + 1, task: task, completed: false })
}

//Save the tasks array into the local storage
const setTodos = () => {
  localStorage.setItem('todos', JSON.stringify(tasks.value))
}

//Get the tasks array saved into the local storage if exist
const getTodos = () => {
  tasks.value = JSON.parse(localStorage.getItem('todos')) ?? []
}

const editTask = (id, value) => {
  const index = tasks.value.findIndex((task) => task.id === id)
  tasks.value[index].task = value
  setTodos()
}

//Set the selected task to pending
const setPending = (id) => {
  const index = tasks.value.findIndex((task) => task.id === id)
  tasks.value[index].completed = false
  setTodos()
}

//Set the selected task to completed
const setCompleted = (id) => {
  const index = tasks.value.findIndex((task) => task.id === id)
  console.log(tasks.value[index])
  tasks.value[index].completed = true
  setTodos()
}

//Delete the selected task in the tasks array
const deleteTask = (id) => {
  const index = tasks.value.findIndex((task) => task.id === id)
  tasks.value.splice(index, 1)
  setTodos()
}

//Delete all the completed tasks in the tasks array
const clearCompleted = () => {
  tasks.value = tasks.value.filter((task) => !task.completed)
  setTodos()
}

//Delete all tasks in the tasks array
const clearTasks = () => {
  tasks.value = []
  setTodos()
}
//Calling the method who retrieve tasks into the local storage on load on the component
onMounted(() => {
  getTodos()
})
</script>
