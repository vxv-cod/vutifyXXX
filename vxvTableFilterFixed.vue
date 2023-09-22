<template >
  <!-- <v-card class="ma-3">filterColumns: {{ filterColumns }}</v-card> -->
  <!-- <v-card class="ma-3">idxListfilter: {{ idxListfilter }}</v-card> -->
  <!-- <v-card class="ma-3"><pre>xxxx: {{ xxxx }}</pre></v-card> -->
  <!-- <v-card class="ma-3"><h2>notfixRef.length: {{ notfixRef.length }}</h2></v-card> -->
  <!-- <v-card class="ma-3"><h2>fixRef.length: {{ fixRef.length }}</h2></v-card> -->
  <!-- <v-card class="ma-3"><h2>keys: {{ keys }}</h2></v-card> -->
  <!-- <v-card class="ma-3"><h2>widths: {{ widths }}</h2></v-card> -->
  <!-- <v-card class="ma-3"><h2>список значений(0, -1): {{ this.widths.slice(0, -1) }}</h2></v-card> -->
  <!-- <v-card class="ma-3"><h2>сумма: {{ this.widths.slice(0, -1).reduce((sum, current) => sum + current, 0) }}</h2></v-card> -->
  <v-card class="ma-3"><h2>rightOffSet: {{ rightOffSet }}</h2></v-card>

<v-card class="pa-3" :color="colorTabBody">
  <div class="d-flex" >
    <v-text-field
      v-model="search"
      prepend-inner-icon="mdi-magnify"
      label="Фильтр по всей таблице"
      single-line
      hide-details
      density="comfortable"
      item-value="name"
    ></v-text-field>
  </div>

  <!-- Задаем таблицу -->
  <v-data-table
    :headers="headers"
    :items="tableRows"
    item-value="name"
    v-model="selectedRows"
    return-object
    items-per-page="10"
    hover
    :search="search"
    fixed-header
    pageText='{0}-{1} из {2}'
    items-per-page-text="Строк"

  >
  <!-- Заполняем заголовоки колонок таблицы -->
  <template v-slot:headers="{ columns, isSorted, getSortIcon, toggleSort, getFixedStyles }" >

    <tr style="box-shadow: 0px 5px 10px #78909C;">
        <td class="bg-blue-grey-lighten-1 pa-0 ma-0 w-0"
          style="position: sticky; left: 0; z-index: 5;"
        >
          <v-checkbox class="d-flex justify-center"
            v-model="checedAllstatus"
            hide-details
            @click="toggleAll"
            :indeterminate="indeterminateState"
          ></v-checkbox>
        </td>
        <td v-for="column in columns" :key="column.key"
            class="bg-blue-grey-lighten-1 "
            :abbr="`${column.title}`" :id="column.key"
            :style="collFix(column)"
            :ref="column.fixed ? 'fixRef' : 'notfixRef'"
            v-resize="onResize"
          >
          <div class="my-flex-container" >
            <div class="my-flex-inner-left ">
              <span class="" @click="() => toggleSort(column)">{{ column.title }}
              </span>
              <!-- ----------------------------------------------- -->
              <span>
                <!-- <pre>column: {{column}}</pre>
                <pre>column.width: {{column.width}}</pre>
                <pre>column.right: {{column.right}}</pre> -->
                <!-- <pre>column.style: {{column.style}}</pre> -->
                <!-- <pre>headers: {{headers}}</pre> -->
                <!-- <pre>idx: {{idx}}</pre> -->
                <!-- style={{getFixedStyles(column)}} -->
              </span>
              <!-- ----------------------------------------------- -->
              <span class="">
                <template v-if="isSorted(column)">
                  <v-icon :icon="getSortIcon(column)"></v-icon>
                </template>
              </span>
            </div>

            <div class="">
              <vxvDialog
                :RowsInColsItem="noDoobleInlist(column.key)"
                :columnKey="column.key"
                :columnTitle="column.title"
                :manualSelects="selectAllFil[column.key]"
                :outSelectedFilter="(value) => columnItem = value"
                :colorBtn="Object.keys(filterColumns).includes(column.key) && column.key !=[] ? `text-amber-accent-3` : null"
                />
            </div>

            <div class="parentTooltip ml-2">
                <v-btn variant="text" density="comfortable"
                  :class="column.fixed ? `text-amber-accent-3` : null"
                  @click="column.fixed = !column.fixed"
                  :icon="column.fixed ? 'mdi-lock-outline' : 'mdi-lock-open-variant-outline'"
                />
                <!-- tooltip ложим в div, при наведении не этот div всплывает подсказка -->
                <v-tooltip activator="parent" location="top" text="Закрепить столбец"></v-tooltip>
            </div>

          </div>
        </td>
      </tr>
    </template>


    <!-- Заполняем строки таблицы -->
    <template v-slot:item="{item, columns}">
      <tr @click="selectedRowsChanged(item)"
        class="bg-blue-grey-lighten-3"
      >
        <td class=""
          :class="selectedRows.includes(item.value) ? `strong bg-${colorTabRow} elevation-0` : null"
          style="position: sticky; left: 0; z-index: 0;
          "
        >
          <v-checkbox class="d-flex justify-center"
            v-model="selectedRows"
            :value="item.value"
            hide-details
            label=""
            style="align-items: center;"
            color="black"
          ></v-checkbox>
        </td>
        <td v-for="(cell, key) in item.value" :key="cell.name"
          :class="selectedRows.includes(item.value) ? `strong bg-${colorTabRow} elevation-0` : null"
          :style="collFix(columns.find(e => e.key === key))"
          >{{ cell }}</td>
        </tr>
    </template>

    <!-- Дополняем footer -->
    <template v-slot:[`footer.prepend`]>
      <div style="margin: 0 20px; color: #FFD740;">
        Вносим любые данные
      </div>
    </template>

<!-- <v-data-table-footer class="bg-red">
  <div>
    <p>oooooooooooo</p>
  </div>
</v-data-table-footer> -->



  </v-data-table>
  </v-card>

  <!-- <v-card><pre>tableRows: {{ tableRows }}</pre></v-card> -->
  <!-- <v-card><pre>selectedRows: {{ selectedRows }}</pre></v-card> -->
  <!-- <v-card><pre>columnItem: {{ columnItem }}</pre></v-card> -->
  <!-- <v-card>tableRows: {{ Object.keys(tableRows) }}</v-card> -->
  <!-- <v-card>filterColumns: {{ filterColumns }}</v-card> -->
  <br>
  <!-- <v-card>RowsInCols: {{ RowsInCols }}</v-card> -->
  <br>
  <!-- <v-card>selectColumns: {{ selectColumns }}</v-card> -->
  <br>



</template>
<script>
  import { computed } from 'vue'
  import vxvDialog from '@/components/vxvTableDialog.vue';
  // import { VDataTable } from 'vuetify/labs/VDataTable'
  import { ref, watchEffect } from 'vue'
import coreJs from 'core-js';

  const fixRef = ref([])
  const notfixRef = ref([])

  export default {
    setup() {
      watchEffect(() => {}, {flush: 'post'})
      return {fixRef, notfixRef}
    },

    components: {
      vxvDialog,
    },

    beforeUpdate () {
    },
    beforeMount () {
        this.tableRows = this.loadTableRows
        this.RowsInCols = structuredClone(this.createRowsInCols)
        Object.keys(this.createRowsInCols).map(e => this.selectAllFil[e] = [])
    },
    mounted () {
      this.onResize()
    },

  methods: {
      toggleAll () {
        if (this.selectedRows.length) {this.selectedRows = []}
        else {this.selectedRows = this.tableRows.slice()}
      },
      selectedRowsChanged(item) {
        let itemValue = item.value
        if (this.selectedRows.includes(itemValue) == false) {this.selectedRows.push(itemValue)}
        else {this.selectedRows.splice(this.selectedRows.indexOf(itemValue), 1)}
        },
      noDoobleInlist(column) {
        // Перебираем список, исключая повторяющиеся значения
        const arr = this.RowsInCols[column].slice()
        return arr.filter((e, i) => arr.indexOf(e) === i)
      },
      collFix(column) {if(column.fixed) {
        return {
          'position':'sticky',
          'z-Index': 4,
          'left':`${column.fixedOffset + 73}px`,
          'right':`${column.right}px`,
          'color': 'white',
          'background-color': '#78909C',
          'outline': '1px solid #ccc',
          'box-shadow': '5px 0px 10px #78909C',
        }}
      },
      onResize() {
        this.fixRef?.forEach(e => {this.headers.forEach((header, idx) => {
            if(header.title === e.abbr) {
              header.width = e.clientWidth
              header.fixed = true
            }
          })})
        },

    },


    watch: {
      search(v) {
        this.tableRows = this.loadTableRows.filter(e => {for (let i in e) {if ((e[i] || '')
        .toString().toLowerCase().includes((v || '').toString().toLowerCase())) {return e}}})
        this.selectedRows = this.selectedRows.filter(e => {if (this.tableRows.includes(e)) {return e}})
      },
      columnItem(item) {
        // Определяем ключ и значение отправленное из диалога фильтра
        let itemKey = Object.keys(item)[0].slice()
        let itemVals = Object.values(item)[0].slice()

        // Собираем объект из выбранных строк в фильтрах по столбцам
        this.filterColumns[itemKey] = itemVals
        // Значение автовыбора всех строк в текущем фильтре

        if(Object.values(this.filterColumns).length >= 1) {
          let key0 = Object.keys(this.filterColumns)[0]
          let Rows0 = this.createRowsInCols[key0].slice()
          this.idxListfilter = []

          // Находим индескы из исходного списка по полученным значениям выбора в фильтрах
          this.createRowsInCols[itemKey].forEach((e, i) => {if (itemVals.includes(e)) {this.idxListfilter.push(i)}})

          // Из полного списка строк в таблице фильтруем те, которые есть в списке выбранных индексов
          this.tableRows = this.loadTableRows.filter((e, i) => this.idxListfilter.includes(i) )

          //  Корректируем фильтра в столбцах, согласно полученным индексам
          Object.entries(this.createRowsInCols).forEach(([key, vals]) => {
            if (key !== itemKey) {
              let arr = vals.filter((e, i) => this.idxListfilter.includes(i))
              this.RowsInCols[key] = arr.slice()
              this.selectAllFil[key] = [... new Set(arr)]
              // Удаляем все фильтра кроме 1го
              if (key !== key0) {delete this.filterColumns[key]}
              } else {this.selectAllFil[key] = itemVals}
            })
          // Для первого фильтра всегда полный список строк
          this.RowsInCols[key0] = [...Rows0]
        }
        // Убираем читсим список для снятия цвета кнопки фильтра
        if(itemVals.length ===  [... new Set(this.RowsInCols[itemKey])].length)
          {delete this.filterColumns[itemKey]}
        this.idxListfilter = []
      },

      'fixRef.length'() {
        this.fixRef?.forEach(e => {this.headers.forEach((header, idx) => {
          if(header.title === e.abbr) {
            header.width = e.clientWidth
            header.fixed = true
        }})})
        // Собираем списки с ширинами колонок и их именами, но из перевернутого списка имен колонок
        let widths = []
        let keys = []
        if(this.fixRef !== null) {this.columnKeysRreverse.forEach(x =>{
          this.fixRef?.forEach(e => {if(x === e.id) {
              widths.push(e.clientWidth)
              keys.push(e.id)
            }})
        })}
        // Определяем отступы от правой границ
        this.rightOffSet = {}
        let sumitem = 0
        keys?.forEach((e, i) => {
          sumitem += widths[i]
          this.headers.forEach(header => {
            if(header.key === e) {header.right = sumitem - widths[i]
          }})
        })
        sumitem = 0
      },

      'notfixRef.length'() {
        this.notfixRef?.forEach(e => {this.headers.forEach((header, idx) => {
          if(header.title === e.abbr && header.width !== undefined) {
            header.fixed = false
            delete header.width
        }})})
      }
    },

    computed: {
      checedAllstatus() {
        let res = null
        if (this.selectedRows.length === this.tableRows.length || this.indeterminateState === true) {res = true}
        if (this.selectedRows.length === 0) {res = false}
        return res
      },
      indeterminateState() {
        return this.selectedRows.length != 0 && this.selectedRows.length < this.tableRows.length? true : false
        // return [0, this.tableRows.length].includes(this.selectedRows.length) === false? true : false
      },
      createRowsInCols() {
        const obj = {}
        this.headers.map(e => e.key).map(col => obj[col] = this.loadTableRows.map(e => e[col]))
        return obj
      },
      columnKeysRreverse() {
        return this.headers.map(e => e.key).reverse()
      },



    },

    data () {
      return {
        drawer: null,
        columnItem: {},
        filterColumns: {},

        search: '',
        colorHeder: "amber-accent-2",
        colorTabHeder: "blue-grey-lighten-1",
        colorTabBody: "blue-grey-lighten-5",
        colorTabRow: "blue-grey-lighten-4",
        loading: false,
        selectedRows: [],
        pagination: { sortBy: 'name'},
        tableRows: [],

        groupBy: [{ key: 'dairy', order: 'asc' }],
        RowsInCols: {},
        selectColumns: [],
        RowsInCols: {},
        selectAllFil: {},
        indexClickFilter: {},
        indexLogObj: {},
        idxListfilter: [],
        xxxx: null,
        stylefixedCollsLeft: "position: sticky; z-index: 4; left: 0",
        stylefixedCollsright: "position: sticky; z-index: 4; right: 0",
        // 'border-color-right': '#78909C'

        windowSize: {},
        rightOffSet: {},

        headers: [
          {
            // title: 'Dessert (100g serving) - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Froze',
            title: 'Dessert (100g serving)',
            align: 'start',
            sortable: false,
            key: 'name',
            groupable: true,
            // fixed: true,
            // fixed: false,
            // width: 350
          },
          { title: 'Calories', key: 'calories', fixed: false},
          { title: 'Fat (g)', key: 'fat', fixed: false},
          { title: 'Carbs (g)', key: 'carbs', fixed: false},
          { title: 'Protein (g)', key: 'protein', fixed: false},
          { title: 'Iron (%)', key: 'iron', fixed: false},
          { title: 'Dairy', key: 'dairy', fixed: false},
        ],
        loadTableRows: [
          {
            name: 'Frozen Yogurt',
            calories: 159,
            fat: 6.0,
            carbs: 24,
            protein: 4.0,
            iron: 1,
            dairy: 'Yes',
          },
          {
            // name: 'Ice cream sandwich',
            name: 'Frozen Yogurt',
            calories: 237,
            fat: 9.0,
            carbs: 37,
            protein: 4.3,
            iron: 1,
            dairy: 'Yes',
          },
          {
            name: 'Eclair',
            calories: 262,
            fat: 16.0,
            carbs: 23,
            protein: 6.0,
            iron: 7,
            dairy: 'Yes',
          },
          {
            name: 'Cupcake',
            calories: 305,
            fat: 3.7,
            carbs: 67,
            protein: 4.3,
            iron: 8,
            dairy: 'No',
          },
          {
            name: 'Gingerbread',
            calories: 356,
            fat: 16.0,
            carbs: 49,
            protein: 3.9,
            iron: 16,
            dairy: 'Yes',
          },
          {
            name: 'Jelly bean',
            calories: 375,
            fat: 0.0,
            carbs: 94,
            protein: 0.0,
            iron: 0,
            dairy: 'No',
          },
          {
            name: 'Lollipop',
            calories: 392,
            fat: 0.2,
            carbs: 98,
            protein: 0,
            iron: 2,
            dairy: 'No',
          },
          {
            name: 'Honeycomb',
            calories: 408,
            fat: 3.2,
            carbs: 87,
            protein: 6.5,
            iron: 45,
            dairy: 'No',
          },
          {
            name: 'Donut',
            calories: 452,
            fat: 25.0,
            carbs: 51,
            protein: 4.9,
            iron: 22,
            dairy: 'No',
          },

          {
            name: 'KitKat',
            calories: 518,
            fat: 26.0,
            carbs: 65,
            protein: 7,
            iron: 6,
            dairy: 'No',
          },

        ],
      }
    },

  }


</script>

<style scope>
  table td {
    padding: 0;
    margin: 0;
    text-align: center;
    outline: 1px solid #cccccc77;
  }
  /* table td {
    outline: 1px solid #ccc;
    background-color: red;
    box-shadow: 0px 5px 10px #78909C;
  } */
  /* table th:first-child {
    outline: 1px solid #ccc;
    background-color: red;
    box-shadow: 0px 5px 10px #78909C;
  } */
  table td:nth-child(2) {
    text-align: left;

  }

.my-flex-container {
  display: flex;
  flex-wrap: nowrap;
  justify-content: center;
  align-items: center;
  position: relative;
}
.my-flex-inner-left {
  flex-grow: 1;
  display: inline-block;
  margin-right: 10px;
}

.v-data-table-footer  {
  position: relative;
  background-color: #78909C;
  color: white;
  outline: 0px solid #78909C;
  box-shadow: 0px -5px 10px #78909C;
  padding: 5px;
}


/* .v-data-table .v-table__wrapper > table tbody > tr > td, .v-data-table .v-table__wrapper > table tbody > tr th { */
/* .v-data-table .v-table__wrapper table tbody tr td { */
  /* background: rgb(var(--v-theme-surface)); */
  /* background: none; */
/* } */

/* .v-data-table-footer__items-per-page,
.v-data-table-footer__items-per-page,
.v-data-table-footer__info,
.v-data-table-footer__pagination,
.v-data-table-footer__page,
.v-data-table-footer span {
  background-color: #CFD8DC;
} */

/* .v-data-table-footer {
  background-color: red;
  box-shadow: 0px -5px 10px #78909C;
  height: auto 20px;
} */

/* .v-data-table .v-table__wrapper > table > thead > tr > td
{
  margin: 0;
  color: rgb(245, 0, 0);
  justify-content: center;
}
 */


</style>



