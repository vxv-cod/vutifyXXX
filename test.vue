<template>
  <div>rows => {{ rows }}</div>
  <div class="q-pa-md">
    <q-table
      flat bordered
      title="Treats"
      :rows="rows"
      :columns="columns"
      row-key="id"
      :sort-method="customSort"
      binary-state-sort
      v-model:pagination="pagination"
      :rows-per-page-options="[15]"

    >
    <template v-slot:top="props">

    <q-btn flat round color="white" size="sm" icon="mdi-pencil"
          @click="showEditIcon = !showEditIcon"
          :class="showEditIcon ? `text-amber` : `text-red`"
        >
        <q-tooltip anchor="top middle" self="bottom middle">Редактировать таблицу</q-tooltip>
      </q-btn>

  </template>

  <!-- todo Заполняем строки таблицы -->
  <template v-slot:body="props">
    <q-tr >

     <q-td v-for="(column, index) in props.cols" :key="column.name" valign="middle"
        :props="props"
        :contenteditable="showEditIcon"

        >
        <div >
          <p v-if="showEditIcon" style="" v-text="column.value"></p>
          <p v-else style="" v-text="column.value"></p>
        </div>
      </q-td>
    </q-tr>
  </template>


  </q-table>

  </div>
  <!--
      :rows-per-page-options="[10]"

   -->
</template>

<script>
import { ref, reactive, watch, toRefs, isRef, onUpdated,
  watchEffect, watchPostEffect, watchSyncEffect
} from 'vue'

import { getCurrentInstance } from 'vue'

import loadRowsCols from "../store/loadRowsCols.json"


const {loadRows, loadColumns} = loadRowsCols

loadColumns[0]["field"] = row => row.name
loadColumns[0]["format"] = val => `${val}`
loadColumns[6]["sort"] = (a, b) => parseInt(a, 10) - parseInt(b, 10)
loadColumns[7]["sort"] = (a, b) => parseInt(a, 10) - parseInt(b, 10)



export default {
  setup () {
  const rows = ref(loadRows)
  const columns = reactive(loadColumns)
  const showEditIcon = ref(false)

  const instance = getCurrentInstance()


  function customSort (rows, sortBy, descending) {
    const data = [...rows]
    if (sortBy) {
      data.sort((a, b) => {
        const x = descending ? b : a
        const y = descending ? a : b
        if (sortBy === 'name') {
          // string sort
          return x[ sortBy ] > y[ sortBy ] ? 1 : x[ sortBy ] < y[ sortBy ] ? -1 : 0
        }
        else {
          // numeric sort
          return parseFloat(x[ sortBy ]) - parseFloat(y[ sortBy ])
        }
      })
    }
    return data
  }

  const pagination = ref({
      sortBy: 'desc',
      descending: false,
      page: 1,
      // rowsPerPage: 4
    })


    // onUpdated(() => {console.log('onUpdated_000')})

    // watchPostEffect (() => {
      // console.log("showEditIcon => ", showEditIcon.value)
      // onUpdated(() => {console.log('onUpdated_111')})
      // console.log('instance = ', instance)
      // instance.proxy.$forceUpdate()
    //   showEditIcon.value
    //   instance.update()
    // })



    return {
      columns,
      rows,
      customSort,
      pagination,
      showEditIcon,

    }
  }
}
</script>
