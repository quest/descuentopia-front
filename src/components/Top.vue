<template>
  <div class="card-group d-sm-none d-md-flex">
    <div class="card" v-for="row in data" :key="row.id">
      <img
        :src="row.store.logo"
        :alt="row.store.name"
        class="card-img-top"
        style="height: 200px"
      />
      <div class="card-body">
        <h5 class="card-title">{{ row.store.name }}</h5>
        <p class="card-text">{{ row.excerpt }}</p>
      </div>
      <div class="card-footer">
        <small class="badge rounded-pill text-bg-success"
          >{{ row.discount }}%</small
        >
        <a class="icon-link float-end" target="_blank" :href="row.url">
          Detalles
        </a>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  props: {
    data: Array,
    default: [],
  },
};
</script>
