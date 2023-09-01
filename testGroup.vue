            <!-- 
              :value="for(let i of item.items) {return i}"
              v-model="selected"
              :value="(item.items[0])"
              :value="eee(item)"
              @click="eee(item)"
            -->
    <!-- <template v-slot:group-header="{ item, columns, toggleGroup, isGroupOpen }">
      <tr >
          <v-checkbox
            hide-details
            label=""
            class="d-inline"
            style="align-items: center; "
            @click="xxx(item)"

          ></v-checkbox>
        <td :colspan="columns.length">


          
          <VBtn
            size="small"
            variant="text"
            :icon="isGroupOpen(item) ? '$expand' : '$next'"
            @click="toggleGroup(item)"
          ></VBtn>
          {{ item.value ? 'Contains gluten' : 'Gluten free' }}
        </td>
      </tr>
    </template> -->
<template>
  <v-data-table
    :headers="headers"
    :items="desserts"
    :group-by="groupBy"
    item-value="name"
    class="elevation-1"
      return-object
      show-select
      v-model="selected"
      @click:row="selectedChanged"
      @update:groupBy="ggg"

  >

  </v-data-table>
    <v-card class="mt-2 pa-2">
      <pre>{{ selected }}</pre>
    </v-card>

</template>
<script>
  export default {
    methods: {
      ggg(val) {
        console.log(val)
      },
      // xxx(val) {
      //   let xxx = val.items
      //   for(let i of xxx) {
      //     let xxx = i.selectable
      //     let itemValue = xxx
      //     if (this.selected.includes(itemValue) == false) {this.selected.push(itemValue)} 
      //     else {this.selected.splice(this.selected.indexOf(itemValue), 1)}
      //   }},
      //   eee(item) {
      //       let arr = []
      //       for(let i of item.items) {arr.push(i.selectable)}
      //       console.log("arr = ", arr)
      //       return arr
      //   },

        selectedChanged(Event, {item}) {
        // console.log("item = ", item)
        // console.log("Event = ", Event)
        // console.log("columns = ", item["columns"])
        // this.selected = item["selectable"]
        let itemValue = item["selectable"]
        if (this.selected.includes(itemValue) == false) {this.selected.push(itemValue)} 
        else {this.selected.splice(this.selected.indexOf(itemValue), 1)}
        },    
    },

    data () {
      return {
        selected: [],
      sortBy: [{ key: 'name', order: 'asc' }],
      groupBy: [{ key: 'dairy', order: 'asc' }],
      headers: [
        {
          title: 'Dessert (100g serving)',
          align: 'start',
          key: 'name',
          groupable: false,
        },
        { title: 'Category', key: 'category', align: 'end' },
        { title: 'Dairy', key: 'dairy', align: 'end' },
      ],
      desserts: [
        {
          name: 'Frozen Yogurt',
          category: 'Ice cream',
          dairy: 'Yes',
        },
        {
          name: 'Ice cream sandwich',
          category: 'Ice cream',
          dairy: 'Yes',
        },
        {
          name: 'Eclair',
          category: 'Cookie',
          dairy: 'Yes',
        },
        {
          name: 'Cupcake',
          category: 'Pastry',
          dairy: 'Yes',
        },
        {
          name: 'Gingerbread',
          category: 'Cookie',
          dairy: 'No',
        },
        {
          name: 'Jelly bean',
          category: 'Candy',
          dairy: 'No',
        },
        {
          name: 'Lollipop',
          category: 'Candy',
          dairy: 'No',
        },
        {
          name: 'Honeycomb',
          category: 'Toffee',
          dairy: 'No',
        },
        {
          name: 'Donut',
          category: 'Pastry',
          dairy: 'Yes',
        },
        {
          name: 'KitKat',
          category: 'Candy',
          dairy: 'Yes',
        },
        ],
      }
    },
  }
</script>


<style scoped>
/* .v-data-table-group-header-row
{
  background-color: red; */
/* } */
</style>