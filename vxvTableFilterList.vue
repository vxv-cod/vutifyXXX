<template>
            <!-- class="bg-blue-grey-lighten-5 strong  ma-0 pa-0" -->

  <v-card
    class="mx-auto"

    max-width="500"
  >
    <v-list select-strategy="classic" lines="one" 
        class="ma-0 pa-0"
      >

        <!-- <v-list-item v-for="item in items" :key="item"
              :value="item"
              v-model="selected"
              @click="editSelected(item)"
              >
              <template v-slot:prepend="{ isActive }">
                  <v-list-item-action start>
                      <v-checkbox-btn 
                          :model-value="isActive" 
                          ></v-checkbox-btn>
                  </v-list-item-action>
              </template>
              {{ item }}
      </v-list-item> -->


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
  <!-- <v-card><pre>selected_Lixt = {{ selected }}</pre></v-card> -->
  <!-- <v-card><pre>resultAll = {{ resultAll }}</pre></v-card> -->
  <!-- <v-card><pre>selected = {{ selected.length }}</pre></v-card> -->
  
  </v-card>
</template>

<script>
  export default {
    inject: [
      'resultAll',
      ],
    props: {
      outputDataList: {
        type: Function,
        default: () => null,
      },      
      items: {
        type: Array,
        default: () => null
      },
      columnKey: {
        type: String,
        default: () => ''
      },
    },

    
    
    mounted () {
        if(this.resultAll[this.columnKey]) 
          {this.selected = this.resultAll[this.columnKey]}
        else {this.selected = []}
    }, 



    computed: {

      // resultAll() {
        // this.selected = this.resultAll[this.columnKey]
        // this.selected = this.resultAll[this.columnKey]
        // console.log("computed = ", this.resultAll[this.columnKey].length)

      // },
    },

    data () {
      return {
        selected: [],
        // selected: [],
      }},    
     
   
    methods: {
      editSelected (val) {
        if (!this.selected.includes(val)) {this.selected.push(val)} 
        else {this.selected.splice(this.selected.indexOf(val), 1)}
        this.outputDataList(this.selected)
      },
    },

    watch: {
      // selected() {
      //   // this.selected = this.resultAll[this.columnKey]
      //   console.log("this.selected Была изменена")
      // },
    },

  }
</script>