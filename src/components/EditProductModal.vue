<template>
    <div id="modal-container">
        <!-- b-modal to that handel edit button -->
    <b-modal :visible="showModal" title="Edit Product" @ok="submitForm" @hidden="cancelEdit">
      <!-- form container -->
      <form @submit.prevent="submitForm">
        <div class="form-group">
          <label for="title">Title</label>
          <b-form-input v-model="localProduct.title" id="title" required />
        </div>
        <div class="form-group">
          <label for="price">Price</label>
          <b-form-input v-model="localProduct.price"  id="price" required />
        </div>
        <div class="form-group">
            <label for="dropdown-category">Select Category</label>       
            <label for="category">Category</label>
          <b-form-input v-model="localProduct.category" id="category" required />
        </div>
        <div class="form-group">
          <label for="image">Image URL</label>
          <b-form-input v-model="localProduct.image" id="image" required />
        </div>
        <template>
          <b-button type="submit" variant="primary">Save</b-button>
          <b-button variant="secondary" @click="cancelEdit">Cancel</b-button>
        </template>
      </form>
    </b-modal>
</div>
  </template>
  
  <script>
  export default {
    props: {
      product: Object,
      showModal: Boolean
    },
    data() {
      return {
        // using spread method assigning to localproduct
        localProduct: { ...this.product }
      };
    },
    watch: {
      product(newProduct) {
        this.localProduct = { ...newProduct };
      },
      showModal(newVal) {
        if (!newVal) {
          this.localProduct = { ...this.product };
        }
      }
    },
    methods: {
      submitForm() {
        this.localProduct.price= Number(this.localProduct.price)
        this.$emit('save-product', this.localProduct);
        this.cancelEdit();
      },
      cancelEdit() {
        this.$emit('close');
      }
    }
  }
  </script>
  
 
  