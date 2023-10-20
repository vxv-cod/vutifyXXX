<template>

  <div class="box">
    <q-btn flat round dense color="white" size="md"
      :icon="`${iconBtn}`" style="" class="q-ml-md "
      >
      <q-tooltip  anchor="top middle" self="bottom middle">{{ menuTitle }}</q-tooltip>

      <q-menu anchor="top right" self="top left" transition-show="jump-right" transition-hide="jump-left"
        @update:model-value="(val) => myUpdateModelValue(val)"
        style="background-color: rgba(0, 0, 0, 0);">

        <div bordered class="" style="">
          <q-card  style="position: sticky; top: 0; z-index: 10;-index"
            class="full-width row no-wrap justify-between items-center content-start bg-amber q-pa-sm "
          >

            <div class="text-subtitle1 block">
              {{ menuTitle }}
            </div>

            <div style="margin-left: 10px" class="">
              <q-btn flat round dense color="black" size="sm" class="q-ml-md"
                :icon="iconSelectAll()" @click="selectsNullOrFull"
                >
                <q-tooltip anchor="top middle" self="bottom middle">Выбрать все</q-tooltip>
              </q-btn>
            </div>

          </q-card>

          <q-card class="">
            <!-- Список значений по колонке -->
            <div class="">

              <q-list>
                <q-item v-for="(item, idx) in columnNames" :key="item" tag="label" v-ripple>

                  <q-item-section side center class="">
                    <q-checkbox v-model="selected"
                      size="sm" dense color="black"
                      :label="columnLabels[idx]"
                      :val="item"
                      />

                  </q-item-section>

                  <!-- <q-item-section>
                    <q-item-label side center v-text="item"></q-item-label>
                  </q-item-section> -->

                  <!-- <q-separator inset /> -->
                </q-item>
              </q-list>
            </div>
          </q-card>
        </div>
      </q-menu>
    </q-btn>
  </div>
  <!-- <q-resize-observer @resize="(size) => {console.log('resize-observer = ', size)}" /> -->

  <!-- <pre> items {{ items }}</pre>
  <pre> selected {{ selected }}</pre> -->
  <!-- <pre> fistSLengthSelected {{ fistSLengthSelected }}</pre>
  <pre> selected {{ selected.length }}</pre> -->

</template>

<script setup>
import { ref, onMounted, watch, watchPostEffect, computed, toRefs, defineProps, defineEmits } from 'vue'

const props = defineProps({
    iconBtn: {
      type: String,
      default: () => 'mdi-help-circle-outline',
    },
    columnNames: {
      type: Array,
      default: () => [],
    },
    menuTitle: {
      type: String,
      default: () => ''
    },
    columnLabels: {
      type: Array,
      default: (_list) => []
    },
    visibleColumns: {
      type: Array,
      default: (_list) => []
    },

});

// Прописываем все имена всех событий двумф способами.
// События для v-model из родителя пишется с приставкой "update:",
//    если v-model не именованная, то по умолчанию событие назвается 'update:modelValue'
const emit = defineEmits([
    'update:menuShow',
    'update:visibleColumns',
])

// const emit = defineEmits({
//   'update:modelValue': {
//     type: Boolean,
//     default: () => false,
//   },
// })


const {
  iconBtn,
  columnNames,
  menuTitle,
  columnLabels,

} = toRefs(props)

const items = ref([])
const selected = ref([])
const fistSLengthSelected = ref([])
onMounted(() => { fistSLengthSelected.value = selected.value.length })

watchPostEffect(() => {[
  items.value = columnNames.value.slice(),

  ]
})



function myUpdateModelValue(bool){
  emit('update:menuShow', bool)   // отправляем сигнал о закрытии блока затемнения
  if (bool === false) {
    // this.search = null

    // Если при выходе из фильтра selected пустой, то заплнить все
    if(selected.value.length === 0) { selected.value = items.value.slice() }
    // Если при хакритии selected изменился после открытия фильтра,
    // т.е. его длина другая, то отправляем из фильтра родителю изменения
    if(fistSLengthSelected.value !== this.selected.length) {
        emit('update:visibleColumns', selected.value)
      }
  } else {fistSLengthSelected.value = this.selected.length}




}

function selectsNullOrFull() {
  if (selected.value.length < items.value.length) {selected.value = items.value.slice(0)}
  else {selected.value = []}
}

function iconSelectAll() {
  if (selected.value.length === 0) {return "mdi-checkbox-multiple-blank-outline"}
  if (selected.value.length > 0 && selected.value.length < items.value.length) {return "mdi-checkbox-multiple-blank-outline"}
  if (selected.value.length === items.value.length) {return "mdi-checkbox-multiple-marked-outline"}
}

</script>



<style lang="sass">
// .box
  // display: flex
  // z-index: 101
</style>
