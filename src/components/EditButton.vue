<template>
  <button @click="editProduct">
    <i class="fa fa-edit"></i> Edit
  </button>
</template>

<script>
import { EventBus } from '../EventBus';

export default {
  
  methods: {
    editProduct() {
      // simple emit option used event bus to travers instruction
      EventBus.$emit('edit-product', this.params.data);
    }
  }
}
</script>

<style>
button {
  border: none;
  background: none;
}
</style>
