<template>
  <div class="flex justify-between">
    <input
      v-model="task"
      placeholder="add a new task.."
      class="px-2 border border-transparent focus:border-primary text-white placeholder placeholder:text-center w-full rounded-l-md out bg-zinc-950 outline-none"
    />
    <button
      @click="emitTask()"
      class="bg-primary text-white uppercase font-bold p-2 px-2 rounded-r-md min-w-12 hover:bg-primary/20 hover:border-primary border border-transparent"
    >
      +
    </button>
  </div>
</template>

<script setup>
import { ref } from 'vue'

const task = ref()
const emit = defineEmits(['addTask'])

//Emiting the add new task action to the parent
const emitTask = () => {
  emit('addTask', task.value)
}
</script>
