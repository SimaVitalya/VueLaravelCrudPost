
<template>
  <v-menu open-on-hover>
    <template v-slot:activator="{ props }">
      <v-btn :loading="loading" color="primary" v-bind="props">
        {{ activeSortTitle }}
      </v-btn>
    </template>
    <v-list>
      <v-list-item v-for="(item, index) in sortItems" :key="index" @click="sort(item)" :ripple="{ class: 'text-purple' }">
        <v-list-item-title>{{ item.title }}</v-list-item-title>
      </v-list-item>
    </v-list>
  </v-menu>
</template>

<script>
export default {
  props: {
    loading: Boolean,
    sortItems: Array,
    activeSortTitle: String,
  },
  methods: {
    sort(item) {
      this.$emit('sort', item);
    },
  },
};
</script>
