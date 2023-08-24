<template>
  <v-toolbar center-affix :color=colorHeder style="box-shadow: 0px 0px 10px #37363689;
  padding: 2px;">
    <v-toolbar-title style="max-width: 220px; " >
          Найдено объектов: {{ items.length }} 
    </v-toolbar-title>
    <v-autocomplete
      v-model="select"
      v-model:search="search"
      :loading="loading"
      :items="items"
      density="comfortable"
      hide-no-data
      hide-details
      label="Поиск объекта"
      variant="outlined"
      prepend-inner-icon="mdi-magnify"
      style="max-width: auto; padding: 0; margin-left: 0px;"
      class="mx-3"
    >
    </v-autocomplete>
    <v-btn icon="mdi-dots-vertical"></v-btn>
  </v-toolbar>

  <!-- <pre>{{itemsAll}}</pre> -->
<!-- style="max-width: auto; padding: 0; margin-left: 0px; 
        box-shadow: inset 0px 0px 10px #37363689;" -->


</template>

<script>

  // import externalData from '@/store/contract.json';

  //  преобразуем входной словарь в список из значений по ключам элементов
  // const itemsAll = []
  // for (const i in externalData) {
  //   itemsAll.push(`${externalData[i]['shifr']} ${externalData[i]['subject']} `)
  // }

  export default {
    props: {
      onChanged: {
        type: Function,
        default: () => null
    },
      externalData: {
        type: Array,
        default: () => null
    },
    },

    data () {
      return {
        colorHeder: "amber-accent-2",
        loading: false,
        search: null,
        select: null,
        items: []
      }
    },

    watch: {
      search (val) {
        val && val !== this.select && this.querySelections(val)
        // если найден индекс в itemsAll, то вытягиваем словарь по нему из externalData
        // и помещаем его в onChanged
        this.isIndexTrue ? this.onChanged(this.externalData[this.itemsAll.indexOf(val)]): ''
      },
    },

    methods: {
      querySelections (v) {
        this.loading = true
        // Simulated ajax query
        setTimeout(() => {
          this.items = this.itemsAll.filter(e => {
            return (e || '').toLowerCase().indexOf((v || '').toLowerCase()) > -1
          })
          this.loading = false
        }, 500)
      },
    },

    computed: {
      itemsAll() {
          const temp = [];
          for (const i in this.externalData) {
            temp.push(`${this.externalData[i]['shifr']} ${this.externalData[i]['subject']} `)
          }
          return temp;
      },
    



      // itemsAllDict() {
      //     const temp = new Object();
      //     for (const i in this.externalData) {
      //       temp[this.externalData[i]['id']] = `${this.externalData[i]['shifr']} ${this.externalData[i]['subject']} `
      //     }
      //     console.log(temp)
      //     return temp;
      // },






      isIndexTrue() {
        // если значение поиска есть в списке itemsAll, то возвращаем истину
        return this.itemsAll.indexOf(this.search) > -1;
      },
    },
  }
  
</script>

<style scoped>

</style>