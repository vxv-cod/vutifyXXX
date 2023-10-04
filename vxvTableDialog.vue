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
        <v-btn :disabled="showEditIcon" class="ml-2" icon="mdi-filter-variant" density="comfortable" variant="text" v-bind="props" :class="colorBtn"/>
        <v-tooltip v-if="!showEditIcon" activator="parent" location="top" text="Фильтр"></v-tooltip>
        <v-tooltip v-if="showEditIcon" activator="parent" location="top" text="Фильтр не доступен в режиме редактирования таблицы"></v-tooltip>
      </template>





          <div > <!-- Контейнер -->
              <!-- Заголовок диалогового окна -->
              <div class="elevation-5 mb-2 bg-amber-accent-3"
                  style="display: flex; justify-content: space-between; align-items: center;"
              >
                <v-card-title >{{ columnTitle }}</v-card-title>
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

      <!-- <div class="d-flex">
        <pre class="mr-4">items = {{ items }}</pre>
        <pre>selected = {{ selected }}</pre>
        <pre>outsideSelect = {{ outsideSelect }}</pre>
      </div> -->
      <!-- <pre>fistSLengthSelected = {{ fistSLengthSelected }}</pre> -->
      <!-- <pre>selected.length = {{ selected.length }}</pre>  -->

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
      showEditIcon: {
        type: Boolean,
        default: () => false,
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
      outsideSelect: {
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
    // RowsInColsItem(val) {
    // //   console.log("val_000 = ", this.columnKey, val)
    //   this.items = val.slice()
    // //   // this.selected = val.slice()
    // },

    RowsInColsItem(val) {this.items = val},
    // outsideSelect(val) {this.selected = val.slice()},
    // outsideSelect(val) {this.selected = val},


    outsideSelect: {
      handler(newValue) {this.selected = newValue},
      deep: true,
    },



  //   outsideSelect: {
  //     handler(newValue, oldValue) {
  //       // let [xxx] = Object.entries(oldValue).filter(([i, e]) => e !== newValue[i])
  //       // let ddd = newValue.find((e, i) => e !== oldValue[i])
  //       // let eee = oldValue.find((e, i) => e !== newValue[i])
  //       // let idx = oldValue.findIndex((e, i) => e !== newValue[i])
  //       if(newValue.length !== oldValue.length) {this.selected = newValue}
  //       let editItem = []
  //       newValue.forEach((e, i) => {if(e !== oldValue[i]) {editItem.push(i, oldValue[i], e)}})
  //       if(editItem.length > 0) {
  //         let [idx, oldElem, newElem] = editItem
  //           console.log("newElem = ", newElem)

  //         //   this.RowsInColsItem[this.RowsInColsItem.indexOf(oldElem)] = newElem
  //           this.items[this.items.indexOf(oldElem)] = newElem
  //           // this.selected.splice(idx, 1, newElem)
  //           this.selected = newValue
  //           // this.outItems(this.items)
  //       }
  //       // !isNaN(value) ? value = Number(value.replace(',', '.')) : null

  //     },
  //   deep: true
  // },





    // outsideSelect: {
    //   handler(val) {val.forEach((e, i) =>
    //     this.selected[i] = e
    //   )},
    //   deep: true,
    // }

    // outsideSelect(newValue, oldValue) {
    //     console.log("newValue = ", [...newValue])
    //     console.log("oldValue = ", [...oldValue])
    // },


  //   outsideSelect(newValue, oldValue) {
  //     // deep: true,
  //     console.log(newValue, oldValue);
  // },

    // 'RowsInColsItem.length'() {this.items = this.RowsInColsItem.slice()},
    // 'outsideSelect.length'() {this.selected = val.slice()},

    // RowsInColsItem: {
    //   handler(val) {val.forEach((e, i) =>
    //       this.items[i] = e
    //     )},
    //   deep: true,
    //   immediate: true
    //   },

    // outsideSelect: {
    //   handler(val) {val.forEach((e, i) =>
    //     this.selected[i] = e
    //   )},
    // deep: true,
    // immediate: true
    // },

    // items: {
    //   handler(val) {val.forEach((e, i) =>
    //     this.selected[i] = e
    //   )},
    //   deep: true,
    //   immediate: true
    // },


    // outsideSelect: {
    //   handler(val) {
    //     this.selected = val.slice()
    //     console.log("val = ", this.columnKey, val.slice())
    //     val.forEach((e, i) => {
    //     //   if(!this.selected.includes(e)){
    //     //   console.log(e, i);
    //     //   // this.selected[i] = e
    //     //   // this.this.items[i] = e
    //     // }
    //     })

    //     // this.items = val
    //     // this.selected = val.slice()
    //   },
    //   deep: true,
    //   immediate: true
    // }


    // items(val) {
    //   let xxx = val.filter(e => !this.selected.includes(e))
    //   console.log("xxx = ", xxx.slice());
    // }



  },

}

</script>

<style lang="sass">
.v-list-item--variant-text .v-list-item__overlay
  background: white

.v-dialog > .v-overlay__content
  position: absolute
  top: 50px
  right: 0




</style>
