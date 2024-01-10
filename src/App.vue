<template>
  <div class="bg-white">
    <Header />
    <div class="relative isolate pt-14">
      <div
        class="absolute inset-x-0 overflow-hidden -top-40 -z-10 transform-gpu blur-3xl sm:-top-80"
        aria-hidden="true"
      >
        <div
          class="relative left-[calc(50%-11rem)] aspect-[1155/678] w-[36.125rem] -translate-x-1/2 rotate-[30deg] bg-gradient-to-tr from-primary to-[#918F90] opacity-30 sm:left-[calc(50%-30rem)] sm:w-[72.1875rem]"
          style="
            clip-path: polygon(
              74.1% 44.1%,
              100% 61.6%,
              97.5% 26.9%,
              85.5% 0.1%,
              80.7% 2%,
              72.5% 32.5%,
              60.2% 62.4%,
              52.4% 68.1%,
              47.5% 58.3%,
              45.2% 34.5%,
              27.5% 76.7%,
              0.1% 64.9%,
              17.9% 100%,
              27.6% 76.8%,
              76.1% 97.7%,
              74.1% 44.1%
            );
          "
        ></div>
      </div>
      <div class="py-24">
        <div class="px-6 mx-auto max-w-7xl lg:px-8">
          <div class="max-w-2xl mx-auto mb-20 text-center">
            <h1 class="text-4xl font-bold tracking-tight text-gray-900 sm:text-6xl">CSV Artikel Verarbeitung</h1>
            <p class="mt-6 text-lg leading-8 text-gray-600">
              Diese App erleichtert die Verarbeitung von Artikel-Daten. Laden Sie die Beispiel-CSV-Datei herunter und
              importieren Sie sie mit nur einem Klick.
            </p>

            <div class="flex items-center justify-center mt-10 gap-x-6">
              <label
                for="fileInput"
                class="rounded-md cursor-pointer bg-primary px-3.5 py-2.5 text-sm font-semibold text-white shadow-sm hover:bg-[#c3141dce] focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2"
              >
                Importieren
                <input id="fileInput" accept=".csv" name="file" type="file" class="hidden" @change="handleFileUpload" />
              </label>

              <a href="" class="text-sm font-semibold leading-6 text-gray-900"> Besipiel CSV Herunterlanden </a>
            </div>
          </div>
          <div class="flow-root">
            <div class="-m-2 rounded-xl bg-gray-600/5 ring-1 ring-inset ring-gray-900/10 lg:-m-4 lg:rounded-2xl lg:p-4">
              <div class="p-4">
                <div v-if="!imported" class="flex flex-col items-center py-8">
                  <img class="w-auto h-20 opacity-20" src="@/assets/img/empty-icon.png" alt="empty state" />
                  <h1 class="mt-4 text-xl font-semibold leading-6 text-gray-900">Keine Daten vorhanden</h1>
                  <p class="mt-2 text-sm text-gray-700">
                    Es scheint, als hättest du noch keine Datei zur Verarbeitung importiert.
                  </p>
                </div>
                <div v-if="imported" class="sm:flex sm:items-center">
                  <div class="sm:flex-auto">
                    <h1 class="text-xl font-semibold leading-6 text-gray-900">Artikel Tabelle</h1>
                    <p class="mt-2 mb-4 text-sm text-gray-700 sm:mb-0">Es ist eine Tabelle über deine Artikel.</p>
                  </div>
                  <div class="flex flex-row">
                    <button
                      @click="createRecord"
                      type="button"
                      class="flex items-center px-4 py-1 mr-4 text-sm font-semibold text-center text-black bg-gray-300 rounded-md shadow-sm hover:bg-gray-300/80 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2"
                    >
                      <svg width="30" height="30" viewBox="0 0 15 15" fill="none" xmlns="http://www.w3.org/2000/svg">
                        <path
                          fill-rule="evenodd"
                          clip-rule="evenodd"
                          d="M7 7V4H8V7H11V8H8V11H7V8H4V7H7Z"
                          fill="black"
                        />
                      </svg>
                      Artikel hinzufügen
                    </button>
                    <button
                      v-if="imported"
                      @click="exportCsv"
                      type="button"
                      class="block px-3 py-2 text-sm font-semibold text-center text-white bg-black rounded-md shadow-sm hover:bg-gray-900 focus-visible:outline focus-visible:outline-2 focus-visible:outline-offset-2"
                    >
                      Exportieren
                    </button>
                  </div>
                </div>
                <div class="flow-root mt-4">
                  <div class="overflow-x-auto">
                    <div class="inline-block min-w-full align-middle">
                      <table v-if="imported" class="min-w-full">
                        <thead>
                          <tr>
                            <th
                              v-for="(header, key) in content.meta.fields"
                              :key="'header-' + key"
                              scope="col"
                              class="py-3.5 pl-4 text-left pr-3 text-sm font-semibold text-gray-900 sm:pl-3"
                            >
                              {{ header }}
                            </th>
                          </tr>
                        </thead>
                        <tbody class="bg-white">
                          <tr v-for="(row, rowKey) in paginatedItems" :key="'row-' + rowKey" class="even:bg-gray-50">
                            <td
                              v-for="(column, columnKey) in content.meta.fields"
                              :key="'row-' + rowKey + '-column-' + columnKey"
                              class="py-4 pl-4 pr-3 text-[12px] font-medium text-gray-900 whitespace-nowrap sm:pl-3"
                            >
                              {{ paginatedItems[rowKey][column] }}
                            </td>
                            <td>
                              <button @click="editRecord(row.Hauptartikelnr)">
                                <svg
                                  class="mx-2 mt-1 feather feather-edit"
                                  fill="none"
                                  height="18"
                                  stroke="currentColor"
                                  stroke-linecap="round"
                                  stroke-linejoin="round"
                                  stroke-width="2"
                                  viewBox="0 0 24 24"
                                  width="18"
                                  xmlns="http://www.w3.org/2000/svg"
                                >
                                  <path d="M11 4H4a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2v-7" />
                                  <path d="M18.5 2.5a2.121 2.121 0 0 1 3 3L12 15l-4 1 1-4 9.5-9.5z" />
                                </svg>
                              </button>
                            </td>
                            <td>
                              <button
                                class="mx-2 mt-1 text-sm font-medium text-red-600"
                                @click="deleteRecord(row.Hauptartikelnr)"
                              >
                                <svg
                                  xmlns="http://www.w3.org/2000/svg"
                                  width="18"
                                  height="18"
                                  viewBox="0 0 24 24"
                                  fill="none"
                                  stroke="currentColor"
                                  stroke-width="2"
                                  stroke-linecap="round"
                                  stroke-linejoin="round"
                                  class="feather feather-trash-2"
                                >
                                  <polyline points="3 6 5 6 21 6"></polyline>
                                  <path
                                    d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"
                                  ></path>
                                  <line x1="10" y1="11" x2="10" y2="17"></line>
                                  <line x1="14" y1="11" x2="14" y2="17"></line>
                                </svg>
                              </button>
                            </td>
                          </tr>
                        </tbody>
                      </table>
                    </div>
                  </div>
                </div>
              </div>
              <nav
                v-if="imported"
                class="flex items-center justify-between px-4 py-3 border-t border-gray-200 sm:px-6"
                aria-label="Pagination"
              >
                <div class="hidden sm:block">
                  <p class="text-sm text-gray-700">
                    Seite
                    <span class="font-medium">{{ pagination.currentPage }}</span>
                    von
                    <span class="font-medium">{{ pagination.totalPages }}</span>
                  </p>
                </div>
                <div class="flex justify-between flex-1 sm:justify-end">
                  <button
                    :disabled="pagination.currentPage == 1"
                    @click="pagination.currentPage = 1"
                    class="relative inline-flex items-center px-3 py-2 text-sm font-semibold text-gray-900 rounded-md bg-white/80 ring-1 ring-inset ring-gray-300 hover:bg-gray-50/80 focus-visible:outline-offset-0"
                  >
                    Erste
                  </button>
                  <button
                    :disabled="pagination.currentPage <= 1"
                    @click="pagination.currentPage--"
                    class="relative inline-flex items-center px-3 py-2 ml-3 text-sm font-semibold text-gray-900 rounded-md bg-white/80 ring-1 ring-inset ring-gray-300 hover:bg-gray-50/80 focus-visible:outline-offset-0"
                  >
                    Vorherige
                  </button>
                  <button
                    :disabled="pagination.currentPage >= pagination.totalPages"
                    @click="pagination.currentPage++"
                    class="relative inline-flex items-center px-3 py-2 ml-3 text-sm font-semibold text-gray-900 rounded-md bg-white/80 ring-1 ring-inset ring-gray-300 hover:bg-gray-50/80 focus-visible:outline-offset-0"
                  >
                    Nächste
                  </button>
                  <button
                    :disabled="pagination.currentPage == pagination.totalPages"
                    @click="pagination.currentPage = Math.ceil(content.data.length / pagination.perPage)"
                    class="relative inline-flex items-center px-3 py-2 ml-3 text-sm font-semibold text-gray-900 rounded-md bg-white/80 ring-1 ring-inset ring-gray-300 hover:bg-gray-50/80 focus-visible:outline-offset-0"
                  >
                    Letzte
                  </button>
                </div>
              </nav>
            </div>
          </div>
        </div>
      </div>
      <div
        class="absolute inset-x-0 top-[calc(100%-13rem)] -z-10 transform-gpu overflow-hidden blur-3xl sm:top-[calc(100%-30rem)]"
        aria-hidden="true"
      >
        <div
          class="relative left-[calc(50%+3rem)] aspect-[1155/678] w-[36.125rem] -translate-x-1/2 bg-gradient-to-tr from-primary to-secondary opacity-30 sm:left-[calc(50%+36rem)] sm:w-[72.1875rem]"
          style="
            clip-path: polygon(
              74.1% 44.1%,
              100% 61.6%,
              97.5% 26.9%,
              85.5% 0.1%,
              80.7% 2%,
              72.5% 32.5%,
              60.2% 62.4%,
              52.4% 68.1%,
              47.5% 58.3%,
              45.2% 34.5%,
              27.5% 76.7%,
              0.1% 64.9%,
              17.9% 100%,
              27.6% 76.8%,
              76.1% 97.7%,
              74.1% 44.1%
            );
          "
        ></div>
      </div>
    </div>
    <div v-if="imported" class="py-10 overflow-hidden bg-white sm:py-10">
      <div class="px-6 mx-auto max-w-7xl lg:px-8">
        <div class="grid grid-cols-1 mx-auto max-w-7xl gap-x-8 gap-y-16 sm:gap-y-20">
          <div class="lg:pt-4">
            <div>
              <h2 class="text-base font-semibold leading-7 text-primary">Unsere Statistik</h2>
              <p class="mt-2 text-3xl font-bold tracking-tight text-gray-900 sm:text-4xl">
                Geschlechterverteilung der Artikel
              </p>
              <p class="mt-6 text-lg leading-8 text-gray-600">
                Im folgenden Tortendiagramm wird der prozentuale Anteil der Artikel nach Geschlecht dargestellt.
              </p>
              <div
                class="p-2 mt-10 -m-2 rounded-xl bg-gray-600/5 ring-1 ring-inset ring-gray-900/10 lg:rounded-2xl lg:p-4"
              >
                <div class="relative isolate py-14">
                  <div class="flex items-center justify-center">
                    <Chart v-if="imported" :chart-data="content.data" />
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    <Footer />

    <!-- Edit Existing Record Modal -->
    <EditCreateModal
      v-if="selectedRowForEdit !== null"
      type="edit"
      :isOpen="openEditModal"
      :fields="content?.meta?.fields"
      :formData="selectedRowForEdit"
      @close="closeEditModal"
      @save-data="onSaveData"
    >
      <template #title> Bestehenden Datensatz bearbeiten </template>
      <template #text> Ändere die Details des bestehenden Datensatzes. </template>
      <template #saveBtnText> Speichern </template>
    </EditCreateModal>

    <!-- Create New Record Modal -->
    <EditCreateModal
      v-if="newRecord !== null"
      type="create"
      :isOpen="openCreateModal"
      :fields="content?.meta?.fields"
      :formData="newRecord"
      @close="closeCreateModal"
      @save-data="onAddRecord"
    >
      <template #title> Neuen Datensatz erstellen </template>
      <template #text> Füge neue Felder für den Datensatz hinzu. </template>
      <template #saveBtnText> Speichern </template>
    </EditCreateModal>
  </div>
</template>

<script setup>
// Importing necessary components and libraries
import Chart from '@/components/Chart.vue'
import Header from '@/components/layout/Header.vue'
import Footer from '@/components/layout/Footer.vue'
import EditCreateModal from '@/components/EditCreateModal.vue'
import { ref, reactive, computed } from 'vue'
import Papa from 'papaparse'

// State variables
const content = ref([])
const imported = ref(false)
const selectedRowForEdit = ref(null)
const newRecord = ref(null)
const openEditModal = ref(false)
const openCreateModal = ref(false)

// Pagination configuration
const pagination = reactive({
  currentPage: 1,
  perPage: 8,
  totalPages: computed(() => {
    if (content.value.data) {
      return Math.ceil(content.value.data.length / pagination.perPage)
    } else {
      return []
    }
  }),
})

// Computed property for paginated items
const paginatedItems = computed(() => {
  const { currentPage, perPage } = pagination
  const start = (currentPage - 1) * perPage
  const stop = start + perPage
  if (content.value.data.length > 0) {
    return content.value.data.slice(start, stop)
  } else {
    return []
  }
})

// File upload handling method
const handleFileUpload = function (event) {
  const file = event.target.files[0]
  Papa.parse(file, {
    skipEmptyLines: true,
    header: true,
    dynamicTyping: true,
    complete: (result) => {
      content.value = result
      imported.value = true
    },
    error: (error) => {
      console.error('Error parsing CSV:', error.message)
    },
  })
}
// Method to create a new record
const createRecord = function () {
  newRecord.value = Object.fromEntries(Object.keys(content.value.data[0]).map((key) => [key, '']))
  openCreateModal.value = true
}

// Method to delete a record
const deleteRecord = function (artikelnr) {
  content.value.data = content.value.data.filter((item) => item.Hauptartikelnr !== artikelnr)
}

// Method to initiate edit operation on a record
const editRecord = function (artikelnr) {
  selectedRowForEdit.value = paginatedItems.value.find((item) => item.Hauptartikelnr === artikelnr)
  openEditModal.value = true
}

// Method to save edited Record
const onSaveData = function (record) {
  const selectedArtikelnr = record.Hauptartikelnr
  const indexToUpdate = content.value.data.findIndex((element) => element.Hauptartikelnr === selectedArtikelnr)

  if (indexToUpdate !== -1) {
    // Replace the element with the new data
    content.value.data[indexToUpdate] = record
  }

  selectedRowForEdit.value = null
  openEditModal.value = false
}

// Method to add a new record
const onAddRecord = function (newItem) {
  let existingItem = content.value.data.find((item) => item.Hauptartikelnr == newItem.Hauptartikelnr)

  if (newItem.Hauptartikelnr.trim() === '') {
    alert('Bitte geben Sie eine "Hauptartikelnr" ein. Diese darf nicht leer sein.')
  } else if (existingItem) {
    alert(
      'Die "Hauptartikelnr" muss individuell sein. Es wurde bereits ein Artikel mit dieser Nummer hinzugefügt. Bitte geben Sie eine neue Hauptartikelnr ein.',
    )
  } else {
    content.value.data = [...content.value.data, newItem]
    newRecord.value = null
    openCreateModal.value = false
  }
}

// Method to close the edit modal
const closeEditModal = function () {
  openEditModal.value = false
  selectedRowForEdit.value = null
}

// Method to close the create modal
const closeCreateModal = function () {
  openCreateModal.value = false
  newRecord.value = null
}

// Method to export data as CSV
const exportCsv = function () {
  const csv = Papa.unparse({
    fields: content.value.meta.fields,
    data: content.value.data,
  })

  const blob = new Blob([csv], { type: 'text/csv' })
  const downloadLink = document.createElement('a')

  downloadLink.href = URL.createObjectURL(blob)
  downloadLink.download = 'data.csv'
  document.body.appendChild(downloadLink)
  downloadLink.click()
  document.body.removeChild(downloadLink)
}
</script>
