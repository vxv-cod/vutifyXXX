<template >
<!-- <div class="spacing-playground pa-3 ma-3 elevation-5 bg-blue-grey-lighten-1"> -->
<!-- <div class="pa-3 bg-blue-grey-lighten-1"> -->
<v-card class="pa-3" 
    :color="colorTabBody"
  
  >
  <div class="d-flex" >
    <!-- <v-divider
      :thickness="3"
      class="ml-2 mr-2 border-opacity-100"
      color="blue-grey-lighten-5"
      vertical
    ></v-divider> -->




    <v-text-field
      v-model="search"
      prepend-inner-icon="mdi-magnify"
      label="Найти"
      single-line
      hide-details
      density="comfortable"
      item-value="name"
    ></v-text-field>


  
  </div>

  <!-- :custom-filter="filterOnlyCapsText" -->
  <!-- Задаем таблицу -->
  <v-data-table 
  

    :headers="headers"
    :items="desserts"
    item-value="name"
    v-model="selected"
    return-object
    items-per-page="5"
    hover
    :search="search"
    fixed-header
    pageText='{0}-{1} из {2}'
 


    items-per-page-text="Количество строк:"

  >
    <template v-slot:top>
      <v-text-field
        v-model="search"
        label="Search (UPPER CASE ONLY)"
        class="pa-4"
      ></v-text-field>
    </template>
    <!-- fixed-footer -->
    <!-- Заполняем заголовоки колонок таблицы -->
    <template v-slot:headers="{ columns, isSorted, getSortIcon, toggleSort }" >
      <tr 
        @click.ctrl.exact="ctrlAndClick"
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
            <div class="flex-container">
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
                    :importItems="dessertsColumn(column.key)"
                    :columnKey="column.key"
                    :columnTitle="column.title"
                    :out="(value) => {return result = {[column.key]: value}}"
                    :class="Object.keys(resultAll).includes(column.key) & column.key !=[] ? `text-amber-accent-3` : null"
                    
                  />
                    <!-- style="color: red;" -->
                    <!-- class="Object.keys(resultAll).length ? text-amber-accent-3 : '' " -->
                    <!-- :class="Object.keys(resultAll).length ? `strong bg-${colorTabRow}` : null" -->

              </div>

            </div>
          </td>
        </template>
      </tr>

    </template>

    <!-- Заполняем строки таблицы -->
    <template v-slot:item="{item}">
      <tr 
          @click="selectedChanged(item)"
      >
        <td 
          width=1
          
          :class="selected.includes(item.value) ? `strong bg-${colorTabRow}` : null"
          >
          <v-checkbox
            v-model="selected"
            :value="item.value"
            hide-details
            label=""
            class="d-inline"
            style="align-items: center; "
          ></v-checkbox>
        </td>

        <td v-for="(cell, key) in item.columns" :key="cell.name"
          :class="selected.includes(item.value) ? `strong bg-${colorTabRow}` : null"
        >
          {{ cell }} key = {{key}} </td>
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
  <v-card><pre>result фильтра в таблице: {{ result }}</pre></v-card>
  <v-card><pre>resultAll фильтра в таблице: {{ resultAll }}</pre></v-card>
  

</template>
<script>
  // import { computed } from 'vue'
  import vxvDialog from '@/components/vxvTableDialog.vue';

  export default {
    components: {
      vxvDialog,
    },    

    // provide() {
    //     return {
    //       resultAll: this.resultAll,
    //     }
    //   },

    // props: {
      // items: {
      //   type: Array,
      //   default: () => null
      // },
    // },  

    methods: {
      // @click.ctrl.exact="ctrlAndClick"

                // filterOnlyCapsText (value, query, item) {
                //         return value != null &&
                //           query != null &&
                //           typeof value === 'string' &&
                //           value.toString().toLocaleUpperCase().indexOf(query) !== -1
                //       },



      // querySelections (v) {
      //     this.loading = true
      //       // Simulated ajax query
      //     setTimeout(() => {
      //       this.items = this.itemsAll.filter(e => {
      //         return (e || '').toLowerCase().indexOf((v || '').toLowerCase()) > -1
      //       })
      //       this.loading = false
      //     }, 500)
      // },

      toggleAll () {
        if (this.selected.length) {
          this.selected = []
          }
        else {
          this.selected = this.desserts.slice()
        }
      },
      selectedChanged(item) {
        // console.log(item)
        let itemValue = item.value
        if (this.selected.includes(itemValue) == false) {this.selected.push(itemValue)} 
        else {this.selected.splice(this.selected.indexOf(itemValue), 1)}
        },
      dessertsColumn(val) {
        const arr = []
          for(let i in this.desserts) 
            {arr.includes(val) === false ? arr.push(this.desserts[i][val]) : null}
          // Собирает список неповторяющихся значений по колонкам
          let uniqueChars = arr.filter((element, index) => {
              return arr.indexOf(element) === index;
          });            
          return uniqueChars
      },
    },


    watch: {
       result(item) {
         this.resultAll[Object.keys(item)[0]] = Object.values(item)[0]
         if(Object.values(item)[0].length == 0) 
            { delete this.resultAll[Object.keys(item)[0]] }
        },
    },

    computed: {
      ctrlAndClick() {
        this.resultAll = {}
        // this.out([])
      },        
        testProvide() {
          return this.desserts.length
        },
        itemsAll() {
          const temp = [];
          for (const i in this.externalData) {
            temp.push(i + " ")
          }
          return temp;
        },
        checedAllstatus() {
          let res = null
          if (this.selected.length === this.desserts.length || this.indeterminateState === true) {res = true}
          if (this.selected.length === 0) {res = false}
          return res
        },
        indeterminateState() {
          return this.selected.length != 0 && this.selected.length < this.desserts.length? true : false
        },
    },

    data () {
      return {
        drawer: null,
        result: null,
        resultAll: {},

        isSelectCol: false,

        search: '',
        colorHeder: "amber-accent-2",
        colorTabHeder: "blue-grey-lighten-1",
        colorTabBody: "blue-grey-lighten-5",
        colorTabRow: "blue-grey-lighten-4",
        loading: false,
        selected: [],
        pagination: { sortBy: 'name'},

        headers: [
          {
            // title: 'Dessert (100g serving) - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Frozen Yogurt - Froze',
            title: 'Dessert (100g serving)', 
            align: 'start',
            sortable: false,
            key: 'name',
          },
          { title: 'Calories', key: 'calories' },
          { title: 'Fat (g)', key: 'fat' },
          { title: 'Carbs (g)', key: 'carbs' },
          { title: 'Protein (g)', key: 'protein' },
          { title: 'Iron (%)', key: 'iron' },
        ],
        desserts: [
          {
            name: 'Frozen Yogurt',
            calories: 159,
            fat: 6.0,
            carbs: 24,
            protein: 4.0,
            iron: 1,
          },
          {
            name: 'Ice cream sandwich',
            calories: 237,
            fat: 9.0,
            carbs: 37,
            protein: 4.3,
            iron: 1,
          },
          {
            name: 'Eclair',
            calories: 262,
            fat: 16.0,
            carbs: 23,
            protein: 6.0,
            iron: 7,
          },
          {
            name: 'Cupcake',
            calories: 305,
            fat: 3.7,
            carbs: 67,
            protein: 4.3,
            iron: 8,
          },
          {
            name: 'Gingerbread',
            calories: 356,
            fat: 16.0,
            carbs: 49,
            protein: 3.9,
            iron: 16,
          },
          {
            name: 'Jelly bean',
            calories: 375,
            fat: 0.0,
            carbs: 94,
            protein: 0.0,
            iron: 0,
          },
          {
            name: 'Lollipop',
            calories: 392,
            fat: 0.2,
            carbs: 98,
            protein: 0,
            iron: 2,
          },
          {
            name: 'Honeycomb',
            calories: 408,
            fat: 3.2,
            carbs: 87,
            protein: 6.5,
            iron: 45,
          },
          {
            name: 'Donut',
            calories: 452,
            fat: 25.0,
            carbs: 51,
            protein: 4.9,
            iron: 22,
          },
          {
            name: 'KitKat',
            calories: 518,
            fat: 26.0,
            carbs: 65,
            protein: 7,
            iron: 6,
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



