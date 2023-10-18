<template>
  <table class="table table-striped table-hover">
    <thead>
      <tr>
        <th
          v-for="header in data.headers"
          :key="header"
          class="col"
          style="white-space: wrap"
        >
          {{ header }}
        </th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(rows, index) in data.rows" :key="index">
        <td
          v-for="(row, td) in rows"
          :key="row"
          class=""
          v-show="td != 5 && td != 4"
          style="white-space: nowrap"
          v-html="row"
        ></td>
      </tr>
    </tbody>
  </table>
</template>
<script>
export default {
  props: {
    data: Object,
    default: {},
  },
};
</script>
