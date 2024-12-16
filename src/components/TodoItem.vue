<template>
  <div class="flex flex-col gap-4">
    <h2 class="text-white">Pending</h2>
    <div class="h-[1px] bg-primary"></div>

    <div v-if="uncompletedTasks.length < 1" class="text-center">
      <span class="text-white text-xs uppercase">You have no pending tasks</span>
    </div>
    <div v-else class="flex flex-col gap-2">
      <TransitionGroup
        enter-active-class="transition-transform duration-200 ease-out"
        enter-from-class="transform translate-y-full opacity-0"
        enter-to-class="transform translate-y-0 opacity-100"
        leave-active-class="transition-transform duration-200 ease-out"
        leave-from-class="transform translate-y-0 opacity-100"
        leave-to-class="transform -translate-y-full opacity-0"
      >
        <div
          v-for="task in uncompletedTasks"
          :key="task.id"
          class="p-2 bg-primary/20 rounded-md border border-primary"
        >
          <div class="flex justify-between items-center">
            <p class="text-white text-sm">{{ task.task }}</p>
            <div class="flex gap-2">
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
        enter-active-class="transition-transform duration-200 ease-out"
        enter-from-class="transform translate-y-full opacity-0"
        enter-to-class="transform translate-y-0 opacity-100"
        leave-active-class="transition-transform duration-200 ease-out"
        leave-from-class="transform translate-y-0 opacity-100"
        leave-to-class="transform -translate-y-full opacity-0"
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
import IconBack from './icons/IconBack.vue'
import { computed } from 'vue'

const props = defineProps(['tasks'])
const emit = defineEmits(['setCompleted', 'deleteTask', 'setPending'])

const completedTasks = computed(() => props.tasks.filter((task) => task.completed))
const uncompletedTasks = computed(() => props.tasks.filter((task) => !task.completed))

const emitPendingTask = (id) => {
  emit('setPending', id)
}

const emitCompletedTask = (id) => {
  emit('setCompleted', id)
}

const emitDeletedTask = (id) => {
  emit('deleteTask', id)
}
</script>
