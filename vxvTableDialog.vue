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
      <div >
        <div class="flex-container elevation-5 mb-2 bg-amber-accent-3" >
          
            <v-card-title 
                
            >
              {{ columnTitle }}
            </v-card-title>
            <!-- <v-icon class="mr-2" icon="mdi-filter-variant" density="comfortable" variant="text"></v-icon> -->
                      
            <v-icon class="mr-2 " icon="mdi-filter-variant-remove" density="comfortable" variant="text"></v-icon>
        </div>
        <vxxListSelects 
            :items="items"
            :columnKey="columnKey"
            :outputDataList="value => {return result = outputDataDialog(value)}"
        />

  <!-- <v-card><pre>{{ result }}</pre></v-card> -->
      
      </div>
  
    </v-dialog>
  </v-row>
  <!-- <pre>{{ result }}</pre> -->
  <!-- <pre>Dialog: {{columnKey}}</pre> -->
  <!-- <pre>Parent: {{countRow}}</pre> -->

</template>
<script>
  import vxxListSelects from '@/components/vxvTableFilterList.vue';

  export default {
    components: {
      vxxListSelects,
    },   

    // provide() {
    //     return {
    //       fff: this.result,
    //     }
    //   },


    // inject: [
    //   'resultAll',
    //   ],

    props: {
      outputDataDialog: {
        type: Function,
        default: () => null,
      },      
      items: {
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

  watch: {

  },

    methods: {
    
    },

    computed: {

    },
    
    data () {
      return {
        result: null,
        dialog: false,
        // selectList: [],
      }
    },
  }
</script>