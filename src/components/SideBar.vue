<template>
    <div>
      <!-- sidebar container -->
      <b-button v-b-toggle.sidebar-right>Add Product</b-button>
      <b-sidebar id="sidebar-right" title="Add Product" right shadow>
        <div class="px-3 py-2">
          <!-- b-form that will handel submit also -->
          <b-form @submit.stop.prevent="handleSubmit">
            <!-- contain all labels and inputfields -->
            <label for="text-product-title">Product Title</label>
            <b-form-input v-model="newProduct.title" type="text" id="text-product-title"></b-form-input>
            <label for="text-price">Price</label>
            <b-form-input v-model="newProduct.price"  id="text-price"></b-form-input>
            <label for="text-category">Category</label>
            <b-form-input v-model="newProduct.category" type="text" id="text-category"></b-form-input>
            <label for="text-image">Product Image</label>
            <b-form-input v-model="newProduct.image" type="text" id="text-image"></b-form-input>
            <b-button variant="outline-secondary" type="submit">Submit</b-button>
          </b-form>
        </div>
      </b-sidebar>
    </div>
  </template>
  
  <script>
  export default {
    name: 'SideBar',
    data() {
      return {
        newProduct: {
          title: '',
          price: '',
          category: '',
          image: '',
        }
      };
    },
    methods: {
      // methods to handle submit 
      handleSubmit() {
        // this uses event bus function to emmit product that is being add by user
        this.$emit('add-product', { ...this.newProduct });
        // Reset form fields
        this.newProduct.price= Number(this.newProduct.price)
        this.newProduct = {
          title: '',
          price: '',
          category: '',
          image: ''
        };
      }
    }
  };
  </script>

  