<template>
  <div class="home container">
    <HelloWorld :chartData="employees"/>
     <div class="table-responsive border">
      <table id="data_table" class="table row-border">
        <thead class="table-light">
          <tr>
            <th>Pickup Date</th>
            <th>Delivered</th>
            <th>Undelivered</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="employee of employees" :key="employee.Name">
            <td>{{ employee.month }}</td>
            <td>{{ employee.delivered }}</td>
            <td>{{ employee.undelivered }}</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'
import $ from "jquery";
import dt from "datatables.net";
// have directly used json format of data from csv
import chartData from "../assets/bar-chart.json";
export default {
  name: 'Home',
  components: {
    HelloWorld
  },
  data() {
    return {
      employees: chartData,
      dTable: null
    };
  },
   mounted() {
    this.generateDataTable();
   },
   methods: {
    generateDataTable() {
      this.dTable = $("#data_table").DataTable({
        columns: [{ orderable: false }, null, null],
        dom:
          "<'row my-3'<'col-sm-12 col-md-6'><'col-sm-12 col-md-6 pe-5'f>>" +
          "<'row'<'col-sm-12'tr>>" +
          "<'row my-1 mx-auto'<'col-sm-12 col-md-4'i><'col-sm-12 col-md-4 ps-5'l><'col-sm-12 col-md-4'p>>",
        pagingType: "full",
        scrollY: "200px",
        scrollCollapse: true,
        language: {
          info: "Showing _START_-_END_ of _TOTAL_ entries",
          lengthMenu: "Show _MENU_ entries per page",
          paginate: {
            first: '<i class="fa fa-step-backward">',
            next: `<i class="fa fa-caret-right">`,
            previous: '<i class="fa fa-caret-left">',
            last: '<i class="fa fa-step-forward">',
          },
        },
      });
    }
  }
}
</script>
<style scoped>
::v-deep .paginate_button {
  border: 1px solid #dee2e6 !important;
}
</style>
