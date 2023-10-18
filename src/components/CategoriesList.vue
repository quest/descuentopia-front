<template>
  <div class="mb-2" v-if="data" :load="log(data)">
    <span v-for="item in data" :key="item.id">
      <input
        v-bind:key="item.id"
        type="radio"
        class="btn-check"
        name="category"
        v-bind:id="item.id"
        v-bind:value="item.value"
        autocomplete="off"
        v-model="model"
      />
      <label class="btn btn-outline-primary m-1 btn-sm" v-bind:for="item.id"
        >{{ item.icon }} {{ item.value }}</label
      >
    </span>
  </div>
</template>
<script>
export default {
  props: {
    data: Array,
    default: [],
    value: {
      type: String,
    },
  },
  computed: {
    model: {
      get() {
        return this.value;
      },
      set(value) {
        this.$emit("input", value);
      },
    },
  },
  methods: {
    log(item) {
      console.log(item);
    },
  },
};
</script>
