<template>
  <div
    class="fixed inset-x-0 bottom-0 px-4 pb-6 sm:inset-0 sm:p-0 sm:flex sm:items-center sm:justify-center"
    :class="isOpen ? '' : 'pointer-events-none'"
  >
    <div>
      <transition
        enter-class="opacity-0"
        enter-active-class="duration-300 ease-out"
        enter-to-class="opacity-100"
        leave-class="opacity-100"
        leave-active-class="duration-200 ease-out"
        leave-to-class="opacity-0"
      >
        <div v-if="isOpen" class="fixed inset-0 transition-opacity">
          <div class="absolute inset-0 bg-gray-500 opacity-75" /></div
      ></transition>
      <transition
        enter-class="translate-y-4 opacity-0 sm:translate-y-0 sm:scale-95"
        enter-active-class="duration-300 ease-out"
        enter-to-class="translate-y-0 opacity-100 sm:scale-100"
        leave-class="translate-y-0 opacity-100 sm:scale-100"
        leave-active-class="duration-200 ease-out"
        leave-to-class="translate-y-4 opacity-0 sm:translate-y-0 sm:scale-95"
      >
        <div
          v-if="isOpen"
          v-on-click-outside="close"
          class="transition-all transform bg-white rounded-lg shadow-xl xs:mx-10"
          role="dialog"
          aria-modal="true"
          aria-labelledby="modal-headline"
        >
          <div class="px-4 pt-5 pb-4 border-b-2 rounded-t-lg sm:p-6 sm:pb-4">
            <div class="sm:flex sm:items-start">
              <div class="mt-3 text-center sm:mt-0 sm:ml-4 sm:text-left">
                <h3 class="text-lg font-medium leading-6 text-gray-900 whitespace-normal">
                  <slot name="title" />
                </h3>
                <div class="mt-2">
                  <p class="text-sm leading-5 text-gray-500 whitespace-normal">
                    <slot name="text" />
                  </p>
                </div>
              </div>
            </div>
          </div>
          <div class="p-8 mx-auto max-h-[50vh] overflow-scroll">
            <div class="relative">
              <div v-if="completedFormData !== null" class="grid grid-cols-1 gap-8 md:grid-cols-2 xl:grid-cols-3">
                <template v-for="(header, key) in fields">
                  <div class="mb-4">
                    <label :for="'edit-' + key" class="block mb-2 font-bold text-gray-700">{{ header }}:</label>
                    <input
                      v-model="completedFormData[header]"
                      :id="'edit-' + key"
                      :disabled="key === 0 && type === 'edit'"
                      class="px-4 py-2 border rounded-md w-80"
                    />
                  </div>
                </template>
              </div>
            </div>
          </div>

          <div class="justify-between px-4 py-3 bg-gray-100 rounded-b-lg sm:px-6 sm:flex sm:flex-row-reverse">
            <span class="flex justify-end w-full rounded-md shadow-sm sm:ml-3 sm:w-auto">
              <div class="flex justify-end my-4">
                <button
                  @click="save"
                  type="button"
                  class="block px-3 mr-1 text-sm font-semibold text-center text-white bg-primary rounded-md shadow-sm hover:bg-[#c3141dce] focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2"
                >
                  <slot name="saveBtnText" />
                </button>

                <button
                  @click="close"
                  class="relative inline-flex items-center px-3 py-2 ml-3 text-sm font-semibold text-gray-900 rounded-md bg-white/80 ring-1 ring-inset ring-gray-300 hover:bg-gray-50/80 focus-visible:outline-offset-0"
                >
                  Zurück
                </button>
              </div>
            </span>
          </div>
        </div>
      </transition>
    </div>
  </div>
</template>

<script setup>
import { vOnClickOutside } from '@vueuse/components'

const emit = defineEmits(['close', 'saveData'])

const props = defineProps({
  isOpen: {
    type: Boolean,
    required: true,
  },
  fields: {
    type: Array,
    required: true,
  },
  formData: {
    type: Object,
    required: true,
  },
  type: {
    type: String,
    required: true,
  },
})

let completedFormData = { ...props.formData }

function close() {
  emit('close', false)
}

function save() {
  emit('saveData', completedFormData)
}
</script>
