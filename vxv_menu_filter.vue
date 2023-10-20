<template>

  <!-- <div class=""> -->
    <q-btn :disable="showEditIcon" flat round dense color="white" size="sm"
      icon="mdi-filter-variant" style="" class="q-ml-md " :class="colorBtn"
      >

      <q-tooltip v-if="!showEditIcon" anchor="top middle" self="bottom middle">Фильтр</q-tooltip>
      <q-tooltip v-if="showEditIcon" anchor="top middle" self="bottom middle">Фильтр не доступен в режиме редактирования таблицы</q-tooltip>

      <q-menu anchor="top right" self="top left" transition-show="jump-right" transition-hide="jump-left"
        @update:model-value="(val) => myUpdateModelValue(val)"
        style="
          /* background-color: rgba(0, 0, 0, 0); */
        ">

        <!--
          @show="menuShow = true"
          @hide="menuShow = false"
          $emit('update:modelValue', $event.target.value)
          @update:model-value="$emit('update:modelValue', modelValue=!modelValue)"
          (val) => $emit('update:menuShow', val)
           @update:model-value="(val) => $emit('update:modelValue', val)"

         -->

        <div bordered class="" style=" max-width: 250px">

          <q-card 
            style="position: sticky; top: 0; z-index: 10;-index"
            class="full-width row no-wrap justify-between items-center content-start bg-amber q-pa-sm "
          >
            <div class=" items-center text-subtitle1">
              {{ columnTitle }}
            </div>
            <q-space />
            <div style="margin-left: 10px">
              <q-btn :disabled="showEditIcon" flat round dense color="black" size="sm" class="q-ml-md"
                :icon="iconSelectAll()" @click="selectsNullOrFull"
                >
                <q-tooltip anchor="top middle" self="bottom middle">Выбрать все</q-tooltip>
              </q-btn>
            </div>
          </q-card>

          <!-- @change="(val) => $emit('update:selected', val)" -->

            <!-- Список значений по колонке -->
            <q-card class="">

              <q-list>
                <q-item v-for="item in items" :key="item" tag="label" v-ripple>

                  <q-item-section side center class="">
                    <q-checkbox v-model="selected"
                      :val="item" size="sm" dense color="black" />
                  </q-item-section>

                  <q-item-section>
                    <q-item-label side center v-text="item"></q-item-label>
                  </q-item-section>

                  <!-- <q-separator inset /> -->
                </q-item>
              </q-list>
            </q-card>

        </div>

      </q-menu>
    </q-btn>
  <!-- </div> -->
  <!-- <q-resize-observer @resize="(size) => {console.log('resize-observer = ', size)}" /> -->

  <!-- <pre> items {{ items }}</pre>
  <pre> selected {{ selected }}</pre> -->
  <!-- <pre> fistSLengthSelected {{ fistSLengthSelected }}</pre>
  <pre> selected {{ selected.length }}</pre> -->

</template>

<script setup>
import { ref, onMounted, watch, watchPostEffect, computed, toRefs, defineProps, defineEmits } from 'vue'

const props = defineProps({
    modelValue: {
      type: Boolean,
      default: () => false,
    },
    RowsInColsItem: {
      type: Array,
      default: () => [],
    },
    columnTitle: {
      type: String,
      default: () => ''
    },
    columnKey: {
      type: String,
      default: () => ''
    },
    colorBtn: {
      type: String,
      default: () => ''
    },
    showEditIcon: {
      type: Boolean,
      default: () => false,
    },
    outsideSelect: {
      type: Array,
      default: (_list) => [... new Set(_list)]
    },
    columnItem: {
      type: Object,
      default: () => {}
    },

});

// Прописываем все имена всех событий двумф способами.
// События для v-model из родителя пишется с приставкой "update:",
//    если v-model не именованная, то по умолчанию событие назвается 'update:modelValue'
const emit = defineEmits([
    'update:menuShow',
    'update:columnItem',
])

// const emit = defineEmits({
//   'update:modelValue': {
//     type: Boolean,
//     default: () => false,
//   },
// })


const indeterminate = ref(false)
const {
  RowsInColsItem,
  outsideSelect,
  columnKey,
  showEditIcon,

} = toRefs(props)

const items = ref([])
const selected = ref([])
const fistSLengthSelected = ref([])
onMounted(() => { fistSLengthSelected.value = selected.value.length })

// console.log("outsideSelect = ", outsideSelect.value)

// watch(() => RowsInColsItem.value, (val) => items.value = val)
// onMounted(() => {
//   selected.value = outsideSelect.value
// })

watchPostEffect(() => {[
  items.value = RowsInColsItem.value.slice(),
  // selected.value = [...new Set(outsideSelect.value)],
  selected.value = outsideSelect.value.slice(),
  // emit('update:outsideSelect', selected.value)
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
        emit('update:columnItem', {[columnKey.value]: selected.value})
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
  if ( selected.value.length === items.value.length) {return "mdi-checkbox-multiple-marked-outline"}
}

</script>



<style lang="sass">
// .box
  // z-index: 101
</style>
