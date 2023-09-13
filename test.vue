<template >
  <!-- <v-card class="ma-3">filterColumns: {{ filterColumns }}</v-card> -->
  <!-- <v-card class="ma-3">idxListfilter: {{ idxListfilter }}</v-card> -->
  <v-card class="ma-3">xxxx: {{ xxxx }}</v-card>

<v-card class="pa-3" :color="colorTabBody">
  <!-- <div class="d-flex" >
    <v-text-field
      v-model="search"
      prepend-inner-icon="mdi-magnify"
      label="Фильтр по всей таблице"
      single-line
      hide-details
      density="comfortable"
      item-value="name"
    ></v-text-field>
  </div> -->

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
    items-per-page-text="Количество строк:"

  >
    <!-- Заполняем заголовоки колонок таблицы -->
    <template v-slot:headers="{ columns, isSorted, getSortIcon, toggleSort, getFixedStyles }" >
      <tr>
        <td class="bg-blue-grey-lighten-1" width=1>
          <v-checkbox
            hide-details
            @click.stop="toggleAll"
            v-model="checedAllstatus"
            :indeterminate="indeterminateState"
            label=""
          ></v-checkbox>
        </td>
        <!-- <template v-for="column in columns" :key="column.key"> -->
        <template v-for="(column, y) in columns" :key="column.key"
        
        >
          <!-- <pre>{{column.key}}</pre> -->
          <td class="bg-blue-grey-lighten-1 " 
          
          
          >
            <div class="flex-container" >
              <div class="flex-inner-left">
                <span class="" align="center" @click="() => toggleSort(column)">{{ column.title }}
{{column}}


                </span>
                  <span @click="() => xxxx = getFixedStyles(column, y=100)">
                    xxxx = {{xxxx}}
                  </span>

                <span class="">
                  <template v-if="isSorted(column)">
                    <v-icon :icon="getSortIcon(column)"></v-icon>
                  </template>
                </span>
              </div>

              <div class="flex-inner-right " >
                  <vxvDialog
                    :RowsInColsItem="noDoobleInlist(column.key)"
                    :columnKey="column.key"
                    :columnTitle="column.title"
                    :manualSelects="selectAllFil[column.key]"
                    :outSelectedFilter="(value) => columnItem = value"
                    :colorBtn="Object.keys(filterColumns).includes(column.key) && column.key !=[] ? `text-amber-accent-3` : null"
                  />
              </div>
            </div>
          </td>
        </template>
      </tr>
    </template>

    <!-- Заполняем строки таблицы -->
    <template v-slot:item="{item}">
      <tr @click="selectedRowsChanged(item)"> 
        <td width=1 :class="selectedRows.includes(item.value) ? `strong bg-${colorTabRow}` : null">
          <v-checkbox
            v-model="selectedRows"
            :value="item.value"
            hide-details
            label=""
            class="d-inline"
            style="align-items: center; "
          ></v-checkbox>
        </td>
        <td v-for="cell in item.columns" :key="cell.name"
          :class="selectedRows.includes(item.value) ? `strong bg-${colorTabRow}` : null"
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


  export default {
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
        console.log(obj);
        return obj
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
        

        headers: [
          {
            // title: 'Dessert (100g serving) - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Froze',
            title: 'Dessert (100g serving)', 
            align: 'start',
            sortable: false,
            key: 'name',
            groupable: true,
          },
          { title: 'Calories', key: 'calories', fixed: true, width: 200 },
          { title: 'Fat (g)', key: 'fat' },
          { title: 'Carbs (g)', key: 'carbs' },
          { title: 'Protein (g)', key: 'protein' },
          { title: 'Iron (%)', key: 'iron' },
          { title: 'Dairy', key: 'dairy' },
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

<style >
  td {
    border-right: 0.5px solid #ccc;
    text-align: center;
    border-bottom: 0.5px solid #ccc;
    }
  td:first-child {
    border-left: 1px solid #ccc;
    }
  td:last-child {
    border-right: 1px solid #ccc;
    }
  table td:nth-child(2) {
    text-align: left;
    }

.flex-container {
  display: flex;
  flex-wrap: nowrap;
  justify-content: center;
  align-items: center;

  position: relative;
}
.flex-inner-left {
  flex-grow: 1;
/* background-color: blue; */
}
.flex-inner-right {
  flex-basis: 30px;

}

/* .flex-inner-right:hover {
  background-color: blue;
} */



.v-data-table-footer  {
  background-color: #78909C;
  color: white;
  border-right: 1px solid #ccc;
  /* position:relative;
  left: 10px; */

}
  h3 {
    margin: 0 20px;
    color: rgb(238, 255, 0);
  }


/* .v-data-table-footer__items-per-page,
.v-data-table-footer__items-per-page,
.v-data-table-footer__info,
.v-data-table-footer__pagination,
.v-data-table-footer__page,
.v-data-table-footer span {
  background-color: #CFD8DC;
} */

.v-data-table-footer__info {
  height: auto 20px;
}

/* .v-data-table .v-table__wrapper > table > thead > tr > td
{
  margin: 0;
  color: rgb(245, 0, 0);
  justify-content: center;
} */



</style>



