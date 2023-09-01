<template>

  <v-row justify="center">
    <v-dialog
      v-model="dialog"
      scrollable
      width="auto"
    >
      <template v-slot:activator="{ props }">
        <v-btn  icon="mdi-filter-variant" density="comfortable" variant="text" v-bind="props"></v-btn>
      </template>
          
          <div > <!-- Контейнер -->
              <!-- Заголовок диалогового окна -->
              <div class="flex-container elevation-5 mb-2 bg-amber-accent-3" 
                  style="display: flex; justify-content: space-between;"
              >
                  <v-card-title >{{ columnTitle }}</v-card-title>
                  <v-btn icon="mdi-filter-variant-remove" density="comfortable" variant="text" 
                    @click="clearChecedFilter"
                  ></v-btn>
              </div>
              
              <v-card class="mx-auto" max-width="500">
                  <!-- Поиск по фильтрам -->
                  <v-text-field
                    v-model="search"
                    prepend-inner-icon="mdi-magnify"
                    label="Найти"
                    single-line
                    hide-details
                    density="comfortable"
                    item-value="name"
                    :loading="loadField"
                    autofocus
                  ></v-text-field>  

                        <!-- class="d-flex ma-0"         -->
                  <!-- Список значений по колонке -->
                  <v-list select-strategy="classic" lines="one" class="ma-0 pa-0 bg-white"
                  >
                    <v-list-item v-for="item in items" :key="item"
                        :value="item"
                        v-model="selected"
                        @click="editSelected(item)"
                        class="d-flex ma-0"
                        >
                        <v-list-item-action start >
                          <v-checkbox-btn 
                              v-model="selected"
                              :value="item"
                              hide-details
                          ></v-checkbox-btn>
                          {{item}}
                        </v-list-item-action>
                    </v-list-item>
                  </v-list>
              </v-card>

          </div> <!-- Контейнер -->
        </v-dialog>
      </v-row>
      <!-- <pre>selected = {{ selected }}</pre> -->
      <!-- <pre>importItems: {{items}}</pre> -->
      <!-- <pre>RowsInCols: {{RowsInCols[columnKey]}}</pre> -->
      <!-- <pre>out: {{ $props.out }}</pre> -->

</template>
<script>
  // import vxxListSelects from '@/components/vxvTableFilterList.vue';

  export default {
    components: {
      // vxxListSelects,
    },   

    // provide() {return {fff: this.result, }},
    // inject: ['fff', ],

    props: {
      out: {
        type: Function,
        default: () => null,
      },      
      importItems: {
        type: Array,
        default: () =>  []
      },
      RowsInCols: {
        type: Object,
        default: () =>  []
      },
      columnKey: {
        type: String,
        default: () => ''
      },
      columnTitle: {
        type: String,
        default: () => ''
      },

    },

    // props: {
    //   title: String,
    //   likes: Number,
    //   isPublished: Boolean,
    //   commentIds: Array,
    //   author: Object,
    //   callback: Function,
    //   contactsPromise: Promise // или любой другой конструктор
    // }    


data () {
      return {
        dialog: false,
        selected: [],
        items: [],
        search: null,
        // setTimeout: null,
        loadField: false,
      }
    },

  beforeUpdate () {
    // this.searchItem= this.items = this.importItems
    this.searchItem= this.items = this.noDoobleInlist

    // console.log("--------- ", this.RowsInCols[this.columnKey].length)
    // let rrr = []
    // for(let i of Object.values(this.RowsInCols)) {rrr.push(i.length)}
    // console.log("rrr- ", rrr)
    // console.log("----max()----- ", Math.max(...rrr))
    // console.log("beforeUpdate")    
  },

  mounted () {
    // Запускается при загрузке страници
        // this.searchItem= this.items = this.importItems
        this.searchItem= this.items = this.noDoobleInlist
    // console.log("mounted")    

  }, 

  // beforeCreate() {
  //   console.log("beforeCreate")    
  // },
  //  created() {
  //   console.log("created")    
  // },
  //   beforeMount() {
  //   console.log("beforeMount")    
  // },
  //   beforeMount() {
  //   console.log("beforeMount")    
  // },
  //   activated() {
  //   console.log("activated")
  // },
  //   deactivated() {
  //   console.log("deactivated")
  // },
  //   beforeUnmount() {
  //   console.log("beforeUnmount")
  // },
  // updated () {
  //   console.log("updated")    
  // },


  methods: {
    editSelected (val) {
      if (!this.selected.includes(val)) {this.selected.push(val)} 
      else {this.selected.splice(this.selected.indexOf(val), 1)}
      this.out(this.selected) 
    },

    clearChecedFilter() {
      this.selected = []
      this.out(this.selected)
    },

  },

  computed: {
      noDoobleInlist() {
        // Перебираем список, исключая повторяющиеся значения
        const arr = this.RowsInCols[this.columnKey]
        let uniqueChars = arr.filter((element, index) => {
            return arr.indexOf(element) === index;
        })
        return uniqueChars
      },    

  // allSelected() {
  //     console.log("--------- ", this.RowsInCols[this.columnKey].length)
  // }
  
  },
    
  watch: {
    search (val) {
        this.loadField = true
        // setTimeout(() => {  
        this.items = this.searchItem.filter((element) => 
              { return (element || '').toString().toLowerCase().indexOf((val || '').toLowerCase()) > -1} )
        // }, 500)
        this.loadField = false
    }
  }, 
}

</script>

<style >
.v-list-item--variant-text .v-list-item__overlay {
  background: white;
}
.v-dialog > .v-overlay__content {
  position: absolute;
  top: 50px;
  right: 0;
}
</style>