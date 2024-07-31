<template>
    <div id="modal-container">
        <!-- use b-modal -->
      <b-modal
        ref="editModal"
        v-model="localShowModal"
        title="Edit Product"
        hide-footer
        @ok="submitForm"
        @hidden="handleHidden"
      >
        <!-- form container -->
        <form @submit.prevent="submitForm">
          <div class="form-group">
            <label for="title">Title</label>
            <b-form-input v-model="localProduct.title" id="title" required />
          </div>
          <div class="form-group">
            <label for="price">Price</label>
            <b-form-input v-model="localProduct.price" id="price" required />
          </div>
          <div class="form-group">
            <label for="category">Category</label>
            <b-form-input v-model="localProduct.category" id="category" required />
          </div>
          <div class="form-group">
            <label for="count">Count</label>
            <b-form-input v-model="localProduct.rating.count" id="count" required />
          </div>
          <div class="form-group">
            <label for="image">Image URL</label>
            <b-form-input v-model="localProduct.image" id="image" required />
          </div>
          <div class="button-container">
            <b-button type="submit" variant="outline-secondary">Save</b-button>
            <b-button variant="outline-secondary" @click="cancelEdit">Cancel</b-button>
          </div>
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
        // spread product in localproduct
        localProduct: { ...this.product },
        localShowModal: this.showModal
      };
    },
    watch: {
        // check whether new product is selected
      product(newProduct) {
        this.localProduct = { ...newProduct };
      },
      showModal(newVal) {
        this.localShowModal = newVal;
        if (newVal) {
          this.$nextTick(() => {
            this.$refs.editModal.show();
          });
        } else {
          this.$refs.editModal.hide();
        }
      }
    },
    methods: {
        // 1.submitform
      submitForm() {
        this.localProduct.price = Number(this.localProduct.price);
        this.$emit('save-product', this.localProduct);
        this.handleHidden();
      },
    //   2.cancel
      cancelEdit() {
        this.handleHidden();
        this.$emit('close');
      },
      handleHidden() {
        this.localShowModal = false;
      }
    }
  }
  </script>
  
  <style scoped>
  .button-container {
    display: flex;
    justify-content: center;
    gap: 20px;
  }
  </style>
  