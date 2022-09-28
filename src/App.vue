<template>
  <the-table
      :data="data"
  />
</template>

<script>
import theTable from "@/components/theTable";
import {reactive} from "vue";

export default {
  name: "App",
  components: { theTable },
  setup() {

    const data = reactive([]);

    for (let i = 1; i < 31; i++) {
      data.push({
        label: "TEST" + i,
        amount: Math.round(50000 * Math.random()),
        date: i+ '.07.2022',
        distance: Math.round(10000 * Math.random()),
      });
    }


    return {
      data
    }
  }
}
</script>
