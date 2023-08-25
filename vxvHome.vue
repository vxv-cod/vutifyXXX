<template>
<div class="wrapperClass">

<!-- <v-card>
  <testPoisk 
    :onChanged="testPoiskChanged"
    :externalData="externalData"
    />
   <div>Родитель: {{ resPoisk }}</div>
  <v-card-title >
    Объекты
    <v-spacer></v-spacer>
    <v-text-field
      v-model="search"
      append-inner-icon="mdi-magnify"
      label="Найти"
      single-line
      hide-details
    ></v-text-field>
  </v-card-title>
  <v-data-table
    hover
    :headers="headers"
    :items="items"
    :search="search"
    v-model="selected"
    item-value="name"
    return-object
    show-select
    items-per-page="3"
    @click:row="testPoiskChangedTable"
  ></v-data-table>
  </v-card> -->

  <!-- <v-card><pre>{{ selected }}</pre></v-card> -->


  <v-card>
    <vxvPoisk 
      :onChanged="valueChild => {return resPoisk = valueChild.id}"
      :selected="selected"
      :externalData="externalData"
    />
   <v-card>Родитель: {{ resPoisk }}</v-card>

<v-card class="d-flex">
  <testBtnTogler class="d-flex"/>
</v-card>
  <vxvTable/>
  
  
  
  <!-- <testVirtualTables/> -->
</v-card>

</div>
</template>

<script>
import items from '@/store/contract.json';
import externalData from '@/store/contract.json';
import vxvPoisk from '@/components/vxvPoisk.vue';
import myFooter from '@/components/myFooter.vue';
import testBtnTogler from '@/components/testBtnTogler.vue';
import testVirtualTables from '@/components/testVirtualTables.vue';

import vxvTable from '@/components/vxvTable.vue';

  export default {
    // props: {
    //   items: {
    //     type: Array,
    //     default: () => null
    //   },
    // },

    components: {
      vxvPoisk,
      vxvTable,
      testVirtualTables,
      myFooter,
      testBtnTogler,
    },    
    data () {
      return {
        externalData: externalData,
        selected: [],
        items,
        resPoisk:'',
        // fileItems: () => import('@/store/contract.json'),
        search: '',
        headers: [
          // {key: 'id', title: 'id', align: 'center', sortable: false},
          {key: 'shifr', title: 'Шифр', align: 'center', sortable: true} ,
          {key: 'subject', title: 'Наименование'},
          // (value: string, query: string, item?: any) => boolean | number | [number, number] | [number, number]
        ],
      }
    },
    methods: {
      testPoiskChanged(val) {
        console.log("id = ", val);
        this.resPoisk = val.id
      },

      testPoiskChangedTable(Event, {item}) {
        let itemValue = item.value
        if (!this.selected.includes(itemValue)) { this.selected.push(itemValue)} 
        else {this.selected.splice(this.selected.indexOf(itemValue), 1)}
      }
    },
    computed: {

    },
  }
</script>


<style scoped>
.fade-enter-active,
.fade-leave-active {
  background-color: red;
  transition: opacity 0.5s;
  /* opacity: 5; */
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

.wrapperClass {
    height: 100%;
    margin:  auto 20px;
  }
</style>