<template >
  <!-- <v-card class="ma-5">createRowsInCols: {{ createRowsInCols }}</v-card> -->

<!-- <div class="spacing-playground pa-3 ma-3 elevation-5 bg-blue-grey-lighten-1"> -->
<!-- <div class="pa-3 bg-blue-grey-lighten-1"> -->
<v-card class="pa-3" 
    :color="colorTabBody"
  >
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

    <!-- :custom-filter="customfilter" -->

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
  <!-- 
    :group-by="groupBy" 
  -->

    <template v-slot:top>
      <!-- <v-text-field
        v-model="search"
        label="Поиск по всей таблице"
        class="pa-4"
      ></v-text-field> -->
    </template>
    <!-- fixed-footer -->
    <!-- Заполняем заголовоки колонок таблицы -->
    <template v-slot:headers="{ columns, isSorted, getSortIcon, toggleSort }" >
      <tr 
        >
        <td 
          class="bg-blue-grey-lighten-1"
          width=1
          
        >
            <v-checkbox
              hide-details
              @click.stop="toggleAll"
              v-model="checedAllstatus"
              :indeterminate="indeterminateState"
              label=""

            >
            </v-checkbox>
        </td>
        <template v-for="column in columns" :key="column.key">
          <!-- <pre>{{column.key}}</pre> -->
          <td class="bg-blue-grey-lighten-1 " >
            <div class="flex-container" >
              <div class="flex-inner-left">
                <span class="" align="center" @click="() => toggleSort(column)">{{ column.title }}</span>
                <span class="">
                  <template v-if="isSorted(column)">
                    <v-icon :icon="getSortIcon(column)"></v-icon>
                  </template>
                </span>
              </div>

              <div class="flex-inner-right " 
              
              >
                  <vxvDialog
                    :RowsInColsItem="noDoobleInlist(column.key)"
                    :columnKey="column.key"
                    :columnTitle="column.title"
                    :selectAll="selectAllFil[column.key]"
                    :outSelectedFilter="(value) => columnItem = value"
                    :colorBtn="Object.keys(filterColumns).includes(column.key) && column.key !=[] ? `text-amber-accent-3` : null"
                    

                  />
              
              <!-- 
                    :class="Object.keys(filterColumns).includes(column.key) && column.key !=[] ? `text-red` : null"

                    :class="Object.keys(filterColumns).includes(column.key) && column.key !=[] ? `text-amber-accent-3` : null"
                    :class="Object.keys(filterColumns).includes(column.key) && column.key !=[] ? `text-amber-accent-3` : null"

                :selectColumns="selectColumns"
                :selectColumns="{selectColumns}"
                :out="(value) => {return selectColumns[column.key] = value}"
                :outSelectedFilter="(value) => {return columnItem = {value}}"
                :class="Object.keys(filterColumns).includes(column.key) && column.key !=[] ? `text-amber-accent-3` : null"

              -->

              </div>

            </div>
          </td>
        </template>
      </tr>

    </template>

    <!-- Заполняем строки таблицы -->
    <template v-slot:item="{item}">
      <tr 
          @click="selectedRowsChanged(item)"
      >
        <td 
          width=1
          :class="selectedRows.includes(item.value) ? `strong bg-${colorTabRow}` : null"
          >
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
          
        >
          {{ cell }}</td>

        <!-- <td></td>
        <td>{{ item.columns.name }}</td>
        <td>{{ item.columns.calories }}</td>
        <td>{{ item.columns.fat }}</td>
        <td>{{ item.columns.carbs }}</td>
        <td>{{ item.columns.protein }}</td>
        <td>{{ item.columns.iron }}</td> -->
      
      </tr>


    </template>

    <!-- Дополняем footer -->
    <template v-slot:[`footer.prepend`]>
      <div style="margin: 0 20px; color: #FFD740;">
        Вносим любые данные
      </div>
    </template>




  </v-data-table>
  </v-card>

  <!-- <v-card>tempvarFilter: {{ tempvarFilter }}</v-card> -->
  <!-- <v-card class="ma-5">tempvarFilter: {{ tempvarFilter }}</v-card> -->
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

    // provide() {
    //     return {
    //       filterColumns: this.filterColumns,
    //     }
    //   },

    // props: {
      // items: {
      //   type: Array,
      //   default: () => null
      // },
    // },  


    beforeUpdate () {

    },

    beforeMount () {
        this.TableRowsAfterSearch = this.loadTableRows
        // this.TableRowsAfterSearch = Object.assign({}, this.loadTableRows)
        this.tableRows = this.loadTableRows
        this.RowsInCols = structuredClone(this.createRowsInCols)
        this.tempvarFilter = structuredClone(this.createRowsInCols)
        // this.selectColumns = this.createRowsInCols
        Object.keys(this.createRowsInCols).map(e => this.selectAllFil[e] = true)


    },

    mounted () {
        // this.tableRows = this.loadTableRows
        // this.TableRowsAfterSearch = this.loadTableRows
        // this.selectColumns = this.createRowsInCols
        // this.RowsInCols = this.createRowsInCols
        
    },

  methods: {
      toggleAll () {
        if (this.selectedRows.length) {
          this.selectedRows = []
          }
        else {
          this.selectedRows = this.tableRows.slice()
        }
      },
      selectedRowsChanged(item) {
        let itemValue = item.value
        if (this.selectedRows.includes(itemValue) == false) {this.selectedRows.push(itemValue)} 
        else {this.selectedRows.splice(this.selectedRows.indexOf(itemValue), 1)}
        },
      dessertsColumn(val) {
        // Собираем список из списков значений колонок
        const arr = []
        for(let i in this.TableRowsAfterSearch) 
            {arr.includes(val) === false ? arr.push(this.TableRowsAfterSearch[i][val]) : null}
        // Перебираем список, исключая повторяющиеся значения
        let uniqueChars = arr.filter((element, index) => {
            return arr.indexOf(element) === index;
        })
        return uniqueChars
      },
      noDoobleInlist(column) {
        // Перебираем список, исключая повторяющиеся значения
        const arr = this.RowsInCols[column].slice()
        let uniqueChars = arr.filter((element, index) => {
            return arr.indexOf(element) === index
        })
        return uniqueChars
      },
    },


    watch: {
      search(v) {
        this.tableRows = this.loadTableRows.filter(e => {
          for (let i in e) {
            if ((e[i] || '').toString().toLowerCase().includes((v || '').toString().toLowerCase())) {return e}
          }
        })
        this.selectedRows = this.selectedRows.filter(e => {
          if (this.tableRows.includes(e)) {return e}
        })

        this.TableRowsAfterSearch = this.tableRows.slice()
      },





    columnItem(item) {
      // Определяем ключ и значение отправленное из диалога фильтра
      let itemKey = Object.keys(item)[0].slice()
      let vals = Object.values(item)[0].slice()

      // Присваиваем в общий массив со всеми объектами из всех фильтров
      this.filterColumns[itemKey] = vals

      // Если массив всех фильтров пустой, то возвращаем все в исходное состояние
      // Если полученные значения от текущего фаильтра пустые или выбраны все строки фильтра, 
      // то удаляем запись в общем массиве фильтров
      this.selectAllFil[itemKey] = false

      // if(vals.length === 0) {delete this.filterColumns[itemKey]}

      if(vals.length === 0 || vals.length === this.noDoobleInlist(itemKey).length)
        {delete this.filterColumns[itemKey]}




      if(Object.values(this.filterColumns).length === 0) {
        this.RowsInCols = structuredClone(this.createRowsInCols)
        // this.tempvarFilter = structuredClone(this.createRowsInCols)
        this.tableRows = this.loadTableRows

      }
      if(Object.values(this.filterColumns).length >= 1) {

        // Список всех индексов по всем фильтрам
          let idxListfilter = []
          Object.entries(this.filterColumns).forEach(
            ([key, vals]) => {
              idxListfilter = []
              // Из полного списка строк в таблице фильтруем
              this.tableRows = this.loadTableRows.filter(e => {if (vals.includes(e[key])) {return e}})

              this.createRowsInCols[key].forEach((vv, iv) => 
                {if (vals.includes(vv) && !idxListfilter.includes(iv)) {
                  idxListfilter.push(iv)

                  }})
            })
        // console.log("idxListfilter = ", idxListfilter)


        // //  Корректируем массив, согласно полученным индексам
          Object.entries(this.createRowsInCols).forEach(
            ([ishodnameCol, ishodvals]) => {

              if (ishodnameCol != itemKey) {
                this.selectAllFil[ishodnameCol] = true
                // this.tempvarFilter[ishodnameCol] = ishodvals.filter((e, i) => 
                this.RowsInCols[ishodnameCol] = ishodvals.filter((e, i) => 
                  {if (idxListfilter.includes(i)) {return ishodvals}}
                  )
                  // this.RowsInCols[ishodnameCol] = this.tempvarFilter[ishodnameCol].slice()

                } 
              else {
              // console.table(this.tempvarFilter[ishodnameCol])

                this.selectAllFil[ishodnameCol] = false
                // this.tempvarFilter[ishodnameCol] = this.RowsInCols[ishodnameCol].slice()
              
              // this.RowsInCols[ishodnameCol] = this.createRowsInCols[ishodnameCol]

                }
              // console.log("this.tempvarFilter = ", this.tempvarFilter)
              
              }
            )


      // console.log(Object.keys(this.filterColumns))
      // console.log(Object.values(this.createRowsInCols["name"]))
      let key0 = Object.keys(this.filterColumns)[0]
      if(Object.values(this.filterColumns).length === 1 && itemKey != key0) {
        // this.RowsInCols[key0] = vals.slice()
        this.selectAllFil[key0] = false
        // this.RowsInCols[key0] = structuredClone(this.createRowsInCols[key0])
        this.RowsInCols[key0] = this.createRowsInCols[key0].slice()
      }
      // console.table("keys = ", Object.values(this.RowsInCols))
      // Не выводит полный список




      }

          // let aaa = {
          //   q: ['111','222','333'],
          //   w: 'www',
          //   e: 'eee',
          // }
          // console.log('aaa1 = ', aaa)

          // let bbb = {}

          // bbb.q = aaa.q.slice()
          // //aaa.q = "888888"

          // // bbb = structuredClone(aaa)
          // console.log('bbb1 = ', bbb, bbb.q)
         
          // //bbb.q = "ffffffffff"
          // bbb.q[1] = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

          // console.log('aaa2 = ', aaa)
          // console.log('bbb2 = ', bbb, bbb.q)      


          // console.log('aaa3 = ', aaa)
          // console.log('bbb3 = ', bbb)
          

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
        },


    // RowsInCols() {
    //   // console.log("RowsInCols")
    //   // const arr = []
    //   // const items = this.loadTableRows
    //   // const ColNameList = Object.keys(items[0])
    //   // for(let col of ColNameList) {
    //   //   let xxx = []; for(let e of items) {xxx.push(e[col])}
    //   //   arr.push({[col]: xxx}) }
    //   // return arr

    //   console.log("RowsInCols")
    //   const arr = {}
    //   const items = this.loadTableRows
    //   const ColNameList = Object.keys(items[0])
    //   for(let col of ColNameList) {
    //     let xxx = []; for(let e of items) {xxx.push(e[col])}
    //     arr[col]= xxx
    //     }
    //   return arr
    // },
    createRowsInCols() {
      const arr = {}
      const items = this.loadTableRows.slice()
      const ColNameList = Object.keys(items[0])
      for(let col of ColNameList) {
        let xxx = []; for(let e of items) {xxx.push(e[col])}
        arr[col]= xxx
        }
      return arr
    },    


    },

    data () {
      return {
        drawer: null,
        columnItem: null,
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
        TableRowsAfterSearch: [],

        groupBy: [{ key: 'dairy', order: 'asc' }],
        RowsInCols: {},
        tempvarFilter: {},
        selectColumns: [],
        RowsInCols: {},
        selectAllFil: {},


        headers: [
          {
            // title: 'Dessert (100g serving) - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Froze',
            title: 'Dessert (100g serving)', 
            align: 'start',
            sortable: false,
            key: 'name',
            groupable: true,
          },
          { title: 'Calories', key: 'calories' },
          { title: 'Fat (g)', key: 'fat' },
          { title: 'Carbs (g)', key: 'carbs' },
          { title: 'Protein (g)', key: 'protein' },
          { title: 'Iron (%)', key: 'iron' },
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



