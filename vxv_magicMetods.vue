<template lang="">
  <div class='block'>

    <input type="text" v-model="aaa">
    <br>
    <input type="text" v-model="rows.name" class='rows'>
    <br>
    <input type="text" v-model="columns[0]" class=''>
    <input type="text" v-model="columns[1]" class=''>

    <pre><p>aaa = {{ aaa }}</p></pre>
    <pre><p>rows = {{ rows }}</p></pre>
    <pre><p>columns = {{ columns }}</p></pre>

  </div>
</template>

<script>
import { ref, reactive, watch, toRefs, isRef,
  watchEffect, watchPostEffect, watchSyncEffect
} from 'vue'
const aaa = ref('')
const columns = reactive(
  ['name', 'calories', 'fat', 'carbs', 'protein', 'sodium', 'calcium', 'iron']
)
const rows = reactive(
  {
    name: 'Frozen Yogurt',
    calories: 159,
    fat: 6.0,
    carbs: 24,
    protein: 4.0,
    sodium: 87,
    calcium: '14%',
    iron: '1%'
  },
)

export default {
  setup(props, context) {


    // Отслеживаем несколько объектов
    // watch(() =>
    //   [rows, aaa], () => {
    //     console.log(rows, aaa.value)
    //   },
    //   { deep: true }
    // )

    // // Отслеживаем изменения во всем объекте, обязательно { deep: true }
    // watch(() => rows, (val) => {console.log(val.name)}, { deep: true })

    // // Отслеживаем ref объект
    // watch(() => aaa.value, (val) => {console.log(val)})
    // // watch(() => aaa.value, (val, old) => {console.log("old = ", old, "val = ", val)})

    // // Отслеживаем одно поле объекта
    // watch(() => rows.name, (val) => {console.log(val)})
    // // watch(() => rows.name, (val, old) => {console.log("old = ", old, "val = ", val)})

    // // Отслеживаем один индекс массива
    // watch(() => columns[0], (val) => {console.log(val)})
    // // watch(() => columns[0], (val, old) => {console.log("old = ", old, "val = ", val)})

    // // Отслеживаем все поля объекта
    // Object.keys(rows).forEach(key => {
    //   watch(() => rows[key], (val) => {console.log(val)})
    //   // watch(() => rows[key], (val, old) => {console.log("old = ", old, "val = ", val)})
    // })

    // // Отслеживаем все поля массива
    // columns.forEach((e, i) => {
    //   watch(() => columns[i], (val) => {console.log(val)})
    //   // watch(() => columns[i], (val, old) => {console.log("old = ", old, "val = ", val)})
    // })

    // watchEffect(() => {console.log(columns)}, {flush: 'post'})



    // watchEffect(() => {console.log("watchEffect => ", columns)}, {flush: 'post'})
    // watchPostEffect (() => {console.log("watchPostEffect => ", columns)})

    // columns.forEach((e, i) => {
    //   // watch(() => columns[i], (val) => {console.log(val)})
    //   watchEffect(() => {console.log("sync ", e, columns[i])}, {flush: 'sync'})
    //   watchPostEffect(() => {console.log("watchPostEffect => ", e, columns[i])})
    //   watchSyncEffect(() => {console.log("watchSyncEffect => ", e, columns[i])})
    // })



    // onMounted(() => { })
    // onBeforeMount(() => { })


    // Обновление компонента
    // import { getCurrentInstance, onUpdated } from 'vue'
    // const instance = getCurrentInstance()
    // onUpdated(() => {console.log('onUpdated_000')})
    // instance.update()



    return {
      aaa,
      rows,
      columns,
    }
  }

}
</script>
<style lang="sass">
.block
  margin: 20px 100px
  .rows
    margin: 20px 0

</style>
