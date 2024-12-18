<template>
  <div class="flex flex-col gap-4">
    <h2 class="text-white">Pending</h2>
    <div class="h-[1px] bg-primary"></div>

    <div v-if="uncompletedTasks.length < 1" class="text-center">
      <span class="text-white text-xs uppercase">You have no pending tasks</span>
    </div>
    <div v-else class="flex flex-col gap-2">
      <TransitionGroup
        enter-active-class="transition-transform transition-opacity duration-200 ease-out"
        enter-from-class="transform translate-y-full opacity-0"
        enter-to-class="transform translate-y-0 opacity-100"
        leave-active-class="transition-transform transition-opacity duration-200 ease-out"
        leave-from-class="opacity-100"
        leave-to-class="opacity-0"
      >
        <div
          v-for="(task, index) in uncompletedTasks"
          :key="task.id"
          class="p-2 bg-primary/20 rounded-md border border-primary"
        >
          <div class="flex justify-between items-center">
            <input
              v-if="editIndex === index"
              :v-model="task.task"
              @blur="exitEdit()"
              @keyup.enter="setEdit($event.target.value, task.id, $event)"
              @keyup="handleColor($event)"
              :class="['edit__input', borderColor]"
              :ref="(el) => (editInputRefs[index] = el)"
              class="edit__input p-2 text-sm mr-2 rounded-md border border-primary bg-transparent focus:border-primary text-white placeholder placeholder:text-center w-full outline-none"
            />
            <p v-else class="text-white text-sm p-2">{{ task.task }}</p>
            <div class="flex gap-2">
              <button class="bg-primary p-1 rounded-md transition hover:scale-110">
                <IconEdit @click="toggleEdit(index)" />
              </button>
              <button class="bg-primary p-1 rounded-md transition hover:scale-110">
                <IconSet @click="emitCompletedTask(task.id)" />
              </button>
              <button
                class="bg-primary p-1 rounded-md transition hover:scale-110"
                @click="emitDeletedTask(task.id)"
              >
                <IconDelete />
              </button>
            </div>
          </div>
        </div>
      </TransitionGroup>
    </div>

    <h2 class="text-white">Completed</h2>
    <div class="h-[1px] bg-primary"></div>
    <div v-if="completedTasks.length < 1" class="mx-auto w-2/3 text-center">
      <span class="text-white text-xs uppercase"
        >You have no pending tasks or everything has been completed</span
      >
    </div>
    <div v-else class="flex flex-col gap-2">
      <TransitionGroup
        enter-active-class="transition-transform transition-opacity duration-200 ease-out"
        enter-from-class="transform translate-y-full opacity-0"
        enter-to-class="transform translate-y-0 opacity-100"
        leave-active-class="transition-transform transition-opacity duration-200 ease-out"
        leave-from-class="transform translate-y-0 opacity-100"
        leave-to-class="transform translate-y-full opacity-0"
      >
        <div
          v-for="task in completedTasks"
          :key="task.id"
          class="p-2 bg-primary/20 rounded-md border border-primary"
        >
          <div class="flex justify-between items-center">
            <p class="text-white text-sm p-1">{{ task.task }}</p>
            <button
              class="bg-primary p-1 rounded-md transition hover:scale-110"
              @click="emitPendingTask(task.id)"
            >
              <IconBack />
            </button>
          </div>
        </div>
      </TransitionGroup>
    </div>
  </div>
</template>

<script setup>
import IconDelete from '@/components/icons/IconDelete.vue'
import IconSet from '@/components/icons/IconSet.vue'
import IconBack from '@/components/icons/IconBack.vue'
import IconEdit from '@/components/icons/IconEdit.vue'
import { computed, ref, nextTick } from 'vue'

const props = defineProps(['tasks'])
const emit = defineEmits(['setCompleted', 'deleteTask', 'setPending', 'editTask'])

const isEditing = ref(false)
const editIndex = ref(null)
const editInputRefs = ref([])
const borderColor = ref(null)

//Filtering the base tasks array passed as a props into two differents array for the display
const completedTasks = computed(() => props.tasks.filter((task) => task.completed))
const uncompletedTasks = computed(() => props.tasks.filter((task) => !task.completed))

//Toggle the correct selected input and focus on it
const toggleEdit = (index) => {
  editIndex.value = index
  nextTick(() => {
    const input = editInputRefs.value[index]
    if (input) input.focus()
  })
}

//Change the border color of the input if the input is empty
const handleColor = (event) => {
  borderColor.value = event.target.value.length === 0 ? '!border-red-500' : '!border-primary'
  console.log(borderColor.value)
}

//Checking if the input is not empty and emiting to the parent
const setEdit = (value, id) => {
  if (value.length < 1) {
    return
  }
  isEditing.value = !isEditing.value
  editIndex.value = null
  emitEdit(value, id)
}

const exitEdit = () => {
  isEditing.value = !isEditing.value
  editIndex.value = null
}

//Emiting the pending, completed and delete task actions to the parent
const emitPendingTask = (id) => {
  emit('setPending', id)
}

const emitCompletedTask = (id) => {
  emit('setCompleted', id)
}

const emitDeletedTask = (id) => {
  emit('deleteTask', id)
}

const emitEdit = (value, id) => {
  emit('editTask', id, value)
}
</script>
