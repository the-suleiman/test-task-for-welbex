<template>

  <select v-model="activeFilter">
    <option disabled value="">Select filter</option>
    <option v-for="filter in filters">{{ filter }}</option>
  </select>

  <select v-model="activeCondition">
    <option disabled value="">Select condition</option>
    <option v-for="condition in conditions">{{ condition }}</option>
  </select>

  <input type="text" placeholder="Input value..." v-model="inputField"/>

  <table class="table table-bordered">
    <thead>
    <tr>
      <th
          v-for="col in table.columns"
          :style="Object.assign({width: 'auto'})"
          @click="sortFunc(col.field)"
      >
        {{ col.label }}
      </th>
    </tr>
    <tr v-for="row in sortedRows">
      <td v-for="col in table.columns">
        {{ row[col.field] }}
      </td>
    </tr>
    </thead>
  </table>
  <p>
    <button @click="previousPage">Previous</button>
    <button @click="nextPage">Next</button>
  </p>
</template>

<script>
import {computed, reactive, ref} from "vue";

export default {
  props: {data: Array},
  name: "theTable",
  setup(props) {
    const table = reactive({
      columns: [
        {
          label: "Date",
          field: "date",
          width: "10%",
          sortable: true,
        },
        {
          label: "Label",
          field: "label",
          width: "10%",
          sortable: true,
        },
        {
          label: "Amount",
          field: "amount",
          width: "15%",
          sortable: true,
        },
        {
          label: "Distance",
          field: "distance",
          width: "15%",
          sortable: true,
        },
      ],
      rows: props.data,
      totalRecordCount: computed(() => {
        return table.rows.length;
      }),
      pageSize: 10,
      currentPage: 1,
      sortable: {
        currentSort: "id",
        currentSortDir: "asc",
      }
    })

    let filters = []
    const activeFilter = ref('')

    const conditions = ['=', '>', '<', '∈']
    const activeCondition = ref('')

    const inputField = ref('')

    table.columns.filter((val) => {
      filters.push(val.field)
    })

    const filteredRows = computed(() => {
      if (activeCondition.value === '') {
        return table.rows
      }
      if (activeCondition.value === '=') {
        return table.rows.filter(row => {
          if (inputField.value.toString() === row[activeFilter.value].toString())
            return true
        });
      }
      if (activeCondition.value === '>') {
        return table.rows.filter(row => {
          if (inputField.value.toString() > row[activeFilter.value].toString())
            return true
        });
      }
      if (activeCondition.value === '<') {
        return table.rows.filter(row => {
          if (inputField.value.toString() < row[activeFilter.value].toString())
            return true
        });
      }
      if (activeCondition.value === '∈') {
        return table.rows.filter(row => {
          if (row[activeFilter.value].toString().includes(inputField.value.toString()))
            return true
        });
      }
    })

    const sortFunc = (s) => {
      if (s === table.sortable.currentSort) {
        table.sortable.currentSortDir = table.sortable.currentSortDir === 'asc' ? 'desc' : 'asc'
      }
      table.sortable.currentSort = s
    }

    let sortedRows = computed(() => {
      return filteredRows.value.sort((a, b) => {
        let modifier = 1
        if (table.sortable.currentSort === 'date') return
        if (table.sortable.currentSortDir === 'desc') modifier = -1
        if (a[table.sortable.currentSort] < b[table.sortable.currentSort]) return -1 * modifier
        if (a[table.sortable.currentSort] > b[table.sortable.currentSort]) return modifier
        return 0
      }).filter((row, index) => {
        let start = (table.currentPage - 1) * table.pageSize
        let end = table.currentPage * table.pageSize
        if (index >= start && index < end) return true
      })
    })

    const nextPage = () => {
      if ((table.currentPage * table.pageSize) < table.totalRecordCount) table.currentPage++
    }

    const previousPage = () => {
      if (table.currentPage > 1) table.currentPage--
    }

    return {
      table,
      sortFunc,
      sortedRows,
      nextPage,
      previousPage,
      filters,
      activeFilter,
      conditions,
      activeCondition,
      inputField,
      filteredRows
    }
  }
}
</script>

<style scoped>
.table {
  width: 100%;
  margin-bottom: 1rem;
  color: #212529;
  border-collapse: collapse;
}

.table-bordered thead td,
.table-bordered thead th {
  border-bottom-width: 2px;
}

.table thead th {
  vertical-align: bottom;
  color: #fff;
  background-color: #343a40;
  border-color: #454d55;
  border-bottom: 2px solid #dee2e6;
  cursor: pointer;
}

.table-bordered td,
.table-bordered th {
  border: 1px solid #dee2e6;
}

.table td,
.table th {
  padding: 0.75rem;
  border-top: 1px solid #dee2e6;
  vertical-align: middle;
}

</style>