<template >
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

              <div class="flex-inner-right" >
                  <vxvDialog
                    :RowsInCols="RowsInCols"
                    :importItems="dessertsColumn(column.key)"
                    :columnKey="column.key"
                    :columnTitle="column.title"
                    :out="(value) => {return columnItem = {[column.key]: value}}"
                    :class="Object.keys(filterColumns).includes(column.key) & column.key !=[] ? `text-amber-accent-3` : null"
                  />
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

  <v-card><pre>tempvarFilter: {{ tempvarFilter }}</pre></v-card>
  <!-- <v-card><pre>tableRows: {{ tableRows }}</pre></v-card> -->
  <!-- <v-card><pre>selectedRows: {{ selectedRows }}</pre></v-card> -->
  <!-- <v-card><pre>RowsInCols: {{ RowsInCols }}</pre></v-card> -->
  <!-- <v-card><pre>columnItem: {{ columnItem }}</pre></v-card> -->
  <v-card><pre>filterColumns: {{ filterColumns }}</pre></v-card>
  

</template>
<script>
  // import { computed } from 'vue'
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
        this.tableRows = this.loadTableRows
        this.RowsInCols = this.createRowsInCols

    },

    mounted () {
        // this.tableRows = this.loadTableRows
        // this.TableRowsAfterSearch = this.loadTableRows
        
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
        console.log(item)
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

    customfilter (value, query, item) {
      // let counter = 0
      // console.log("-----------------------------")
      // counter = counter + 1
      // // console.log("value = ", value)
      // // console.log("query = ", query)
      // console.log("item = ", item)
      //   // return value != null &&
      //   //   query != null &&
      //   //   typeof value === 'string' &&
      //   //   value.toString().toLocaleUpperCase().indexOf(query) !== -1
      // console.log("============================", counter)

      // if (value == null || query == null) return -1;
      // console.log("xxx = ", value.toString().toLocaleLowerCase().indexOf(query.toString().toLocaleLowerCase()) )
      // console.log("item = ", item)
      // return value.toString().toLocaleLowerCase().indexOf(query.toString().toLocaleLowerCase());


      // console.log("value = ", value)

      // const resArray = []
      // for (const cell of item) {
      //     if (query.toString() in cell.toString() ) {
      //     resArray.push(item)
      //     // console.log(resArray)

      //     }
      // }

      const resArray = []

      let idx = ''
      if (value == null || query == null) return idx = -1;
      idx = value.toString().toLocaleLowerCase().indexOf(query.toString().toLocaleLowerCase())
      
      if (idx > -1) {
        console.log(this.tableRows[idx+1])
        }
      return idx

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

        this.TableRowsAfterSearch = this.tableRows
      },


      // search(v) {
        // this.select = this.tableRows.filter(e => {
        //   console.log(e.name)
        //     return (e.name || '').toLowerCase().indexOf((v || '').toLowerCase()) > -1
        //     console.log(e)
        //   })
        // this.tableRows


          
              // for (const item of this.tableRows) 

              //     {
              //       console.log(`fff =`, Object.values(item))
              //       // if ((item || '').toString().toLowerCase().indexOf((v || '').toLowerCase()) > -1) {
              //       //   console.log(`fff =`, item)
              //       // }
              //     }

          // for (const item of this.tableRows) {
          //   if (item.includes(item[v])) 
          //       {console.log(`fff =`, item)}
          //       }

    // },





    columnItem(item) {
      // Определяем ключ и значение отправленное из диалога фильтра
      let itemKey = Object.keys(item)[0]
      let vals = Object.values(item)[0]
      // Присваиваем в общий массив со всеми объектами из всех фильтров
      this.filterColumns[itemKey] = vals
      // Если полученные значения от текущего фаильтра пустые, то удаляем запись в общем массиве фильтров
      if(vals.length === 0) {delete this.filterColumns[itemKey]}
      // Если массив всех фильтров пустой, то возвращаем все в исходное состояние
      if(Object.values(this.filterColumns).length === 0) {
        this.tableRows = this.loadTableRows
        this.RowsInCols = this.createRowsInCols
        this.tempvarFilter = {}        
      }
      else {
        // Список всех индексов по всем фильтрам
        let idxListfilter = []
        //  Для каждого фильтра из общего массива
        for (let [kf, valFilCol] of Object.entries(this.filterColumns)) {
          idxListfilter = []
          // Из полного списка строк в таблице фильтруем
          this.tableRows = this.loadTableRows.filter(e => {if (valFilCol.includes(e[kf])) {return e}})  
          for (let [ishodnameCol, ishodvals] of Object.entries(this.createRowsInCols)) {
            for (const [iv, vv] of ishodvals.entries()) {
              if (valFilCol.includes(vv) && !idxListfilter.includes(iv)) {idxListfilter.push(iv)}
              }
          }
        }
        console.log("idxListfilter = ", idxListfilter)

        //  Корректируем массив, согласно полученным индексам
        for (let [ishodnameCol, ishodvals] of Object.entries(this.createRowsInCols)) {
          if (ishodnameCol != itemKey) {
              let fil = ishodvals.filter((e, i) => {if (idxListfilter.includes(i)) {return ishodvals}})
              this.tempvarFilter[ishodnameCol] = fil
          } else {this.tempvarFilter[ishodnameCol] = this.RowsInCols[ishodnameCol]}
        }

        this.RowsInCols = this.tempvarFilter
        // console.log("this.tempvarFilter = ", this.tempvarFilter)
      }
      
      
      
      // else {
      //   // Список всех индексов по всем фильтрам
      //   let idxListfilter = []
      //   //  Для каждого фильтра из общего массива
      //   for (let [kf, valFilCol] of Object.entries(this.filterColumns)) {
      //     // Из полного списка строк в таблице фильтруем
      //     this.tableRows = this.loadTableRows.filter(e => {if (valFilCol.includes(e[kf])) {return e}})  
      //     for (let [ishodnameCol, ishodvals] of Object.entries(this.createRowsInCols)) {
      //       // for (const [iv, vv] of ishodvals.entries()) {
      //       //   if (valFilCol.includes(vv)) {idxListfilter.push(iv)}
      //       //   }
      //     // console.log("ishodvals = ", ishodvals)
      //     // console.log("valFilCol = ", valFilCol)
      //           console.log("ishodnameCol === kf = ", ishodnameCol, kf, ishodnameCol === kf)
          
          
      //     // if (ishodnameCol != itemKey) {
      //         idxListfilter = ishodvals.filter((valIshod, idxIshod) => {
      //             if (
      //               ishodnameCol === itemKey &&
      //               valFilCol.includes(valIshod)
      //               ) {
      //                 return idxIshod}
      //               console.log("ishodnameCol === kf = ", ishodnameCol , kf)
      //           })
      //     // } else {this.tempvarFilter[ishodnameCol] = this.RowsInCols[ishodnameCol]}
      //     // console.log("this.tempvarFilter = ", this.tempvarFilter)
          
      //     }
      //   }
        
      //   // // Из полного списка строк в таблице фильтруем
      //   // this.tableRows = this.loadTableRows.filter((e, i) => {if (idxListfilter.includes(i)) {return e}})  

      //   //  Корректируем массив, согласно полученным индексам
      //   // for (let [ishodnameCol, ishodvals] of Object.entries(this.createRowsInCols)) {
      //   //   if (ishodnameCol != itemKey) {
      //   //       let fil = ishodvals.filter((e, i) => {if (idxListfilter.includes(i)) {return ishodvals}})
      //   //       this.tempvarFilter[ishodnameCol] = fil
      //   //   } else {this.tempvarFilter[ishodnameCol] = this.RowsInCols[ishodnameCol]}
      //   // }

      //   this.RowsInCols = this.tempvarFilter
      //   console.log("this.tempvarFilter = ", this.tempvarFilter)
      // }
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
      console.log("RowsInCols")
      const arr = {}
      const items = this.loadTableRows
      const ColNameList = Object.keys(items[0])
      for(let col of ColNameList) {
        let xxx = []; for(let e of items) {xxx.push(e[col])}
        arr[col]= xxx
        }
      return this.RowsInCols = arr
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
        RowsInCols: [],
        tempvarFilter: {},


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



