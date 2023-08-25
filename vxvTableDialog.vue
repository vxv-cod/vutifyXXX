<template>

  <v-row justify="center">
    <v-dialog
      v-model="dialog"
      scrollable
      width="auto"
    >
      <template v-slot:activator="{ props }">
        <v-btn icon="mdi-filter-variant" density="comfortable" variant="text" v-bind="props"></v-btn>
      </template>
          
          <div > <!-- Контейнер -->
              
              <!-- Заголовок диалогового окна -->
              <div class="flex-container elevation-5 mb-2 bg-amber-accent-3" >
                  <v-card-title >{{ columnTitle }}</v-card-title>
                  <v-icon class="mr-2 " icon="mdi-filter-variant-remove" density="comfortable" variant="text"></v-icon>
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
                                
                  <!-- Список значений по колонке -->
                  <v-list select-strategy="classic" lines="one" class="ma-0 pa-0">
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
      <!-- <pre>{{ result }}</pre> -->
      <!-- <pre>searchItem: {{searchItem}}</pre> -->
      <!-- <pre>Parent: {{countRow}}</pre> -->

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
        resultAll: {},
        items: [],
        search: null,
        setTimeout: null,
        loadField: false,
      }
    },


  mounted () {
      if(this.resultAll[this.columnKey]) 
        {this.selected = this.resultAll[this.columnKey]}
      else {this.selected = []}
   setTimeout(() => this.searchItem = this.importItems)
   setTimeout(() => this.items = this.importItems)
  }, 


    methods: {
      editSelected (val) {
        if (!this.selected.includes(val)) {this.selected.push(val)} 
        else {this.selected.splice(this.selected.indexOf(val), 1)}
        this.out(this.selected) 
      },    
    },

    computed: {

    },
    
    
  watch: {
      // search (v) {
      //   this.loading = true
      //   // Simulated ajax query
      //   setTimeout(() => {
      //     this.searchItem = this.items.filter(e => {
      //       return (e || '').toLowerCase().indexOf((v || '').toLowerCase()) > -1
      //     })
      //     this.loading = false
      //   }, 500)
      // },
      
  search (val) {
      this.loadField = true        
      let res = this.searchItem.filter((element) => 
            { return (element || '').toString().toLowerCase().indexOf((val || '').toLowerCase()) > -1} )
      console.log(res)     
      this.items = res
      this.loadField = false


  }
  }, 
  }

</script>