<template>
  <v-row justify="center" >
    <v-menu
      v-model="menu"
      :close-on-content-click="false"
      location="end"
      max-width="auto"
      scrim
      @update:modelValue="myUpdateModelValue"
    >
      <template v-slot:activator="{ props }" >
        <v-btn  icon="mdi-filter-variant" density="comfortable" variant="text" v-bind="props" :class="colorBtn" />
      </template>
          <div > <!-- Контейнер -->
              <!-- Заголовок диалогового окна -->
              <div class="flex-container elevation-5 mb-2 bg-amber-accent-3" 
                  style="display: flex; justify-content: space-between;"
              >
                <v-card-title >{{ columnTitle }}</v-card-title>
                <!-- checkbox-multiple-blank-outline -->
                <v-btn  density="comfortable" variant="text" 
                  :icon="iconSelectAll"
                  @click="selectsNullOrFull"
                >
                  <v-tooltip activator="parent" location="end" text="Выбрать все"/>                  
                  <v-icon>{{iconSelectAll}}</v-icon>
                </v-btn>
              </div>

              <v-card max-width="500" >
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

                  <!-- Список значений по колонке -->
                  <v-list select-strategy="classic" lines="one" 
                      class="overflow-y-auto ma-0 pa-0 bg-white"
                      max-height="800"
                      style="backdrop-filter: saturate(0)"
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
        </v-menu>
      </v-row>
      
      <!-- <pre>items = {{ items }}</pre>
      <pre>selected = {{ selected }}</pre>
      <pre>fistSLengthSelected = {{ fistSLengthSelected }}</pre>
      <pre>selected.length = {{ selected.length }}</pre> -->

</template>
<script>

  export default {

    // provide() {return {fff: this.result, }},
    // inject: ['fff', ],

    props: {
      outSelectedFilter: {
        type: Function,
        default: () => null,
      },      
      RowsInColsItem: {
        type: Array,
        default: () => []
      },
      columnKey: {
        type: String,
        default: () => ''
      },
      columnTitle: {
        type: String,
        default: () => ''
      },
      colorBtn: {
        type: String,
        default: () => ''
      },
      manualSelects: {
        type: Array,
        default: () => []
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
        menu: false,
        selected: [],
        items: [],
        search: null,
        // setTimeout: null,
        loadField: false,
        fistSLengthSelected: null,
      }
    },

  beforeUpdate () {
  },
  created() {
  },
  mounted () {
    // console.log(this.columnKey, "mounted")
    // Запускается при загрузке страници
    this.searchItem = this.RowsInColsItem.slice()
    this.items = this.RowsInColsItem.slice()
    this.selected = this.RowsInColsItem.slice()
    this.fistSLengthSelected = this.selected.length
  }, 

  // beforeCreate() {
  //   console.log(this.columnKey, "beforeCreate")    
  // },
  //  created() {
  //   console.log(this.columnKey, "created")    
  // },
  //   beforeMount() {
  //   console.log(this.columnKey, "beforeMount")    
  // },
  //   beforeMount() {
  //   console.log(this.columnKey, "beforeMount")    
  // },
  //   activated() {
  //   console.log(this.columnKey, "activated")
  // },
  //   deactivated() {
  //   console.log(this.columnKey, "deactivated")
  // },
  //   beforeUnmount() {
  //   console.log(this.columnKey, "beforeUnmount")
  // },
  // updated () {
  //   console.log(this.columnKey, "updated")    
  // },

  methods: {
    editSelected (val) {
      if (this.selected.includes(val) === false) {this.selected.push(val)} 
      else {this.selected.splice(this.selected.indexOf(val), 1)}
    },
    selectsNullOrFull() {
      if (this.selected.length < this.items.length) {this.selected = this.items.slice(0)}  
      else {this.selected = []}
    },
    myUpdateModelValue(bool){
      if (bool === false) { this.search = null
        if(this.selected.length === 0) {this.selected = this.items.slice()}
        if(this.fistSLengthSelected !== this.selected.length)
            {this.outSelectedFilter({[this.columnKey]: this.selected})}
      } else {this.fistSLengthSelected = this.selected.length}
    },

  },

  computed: {
    iconSelectAll() {
      if (this.selected.length === 0) {return "mdi-checkbox-multiple-blank-outline"}
      if (this.selected.length > 0 && this.selected.length < this.items.length) {return "mdi-checkbox-multiple-blank"}
      if ( this.selected.length === this.items.length) {return "mdi-checkbox-multiple-marked-outline"}
    },
      
  },
    
  watch: {
    search (val) {
      this.loadField = true
      // setTimeout(() => {  
      this.items = this.searchItem.filter((element) => 
            { return (element || '').toString().toLowerCase().indexOf((val || '').toLowerCase()) > -1} )
      // }, 500)
      this.selected = this.items.slice()
      this.loadField = false
    },
    RowsInColsItem(val) {this.items = val},
    manualSelects(val) {this.selected = val.slice()},
    // menu(val) {console.log(val)},
  
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