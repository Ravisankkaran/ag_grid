<template>
  <div id="app">
    <div class="container">
      <!-- side-bar for adding products -->
      <div class="sidebar-container"><side-bar @add-product="addProductToGrid" /></div>
      <!-- ag-grid tables defining all themes and basic styles -->
      <ag-grid-vue
        class="ag-theme-alpine"
        style="height: 500px;"
        :columnDefs="columnDefs"
        :rowData="rowData"
        @grid-ready="onGridReady"
      >
      </ag-grid-vue>
      <!-- when edit button is clicked on ag-grid column this modal opens -->
      <EditModal :product="selectedProduct" :showModal="showModal" @save-product="saveProduct" @close="closeModal" />
    </div>
  </div>
</template>

<script>
// import components
import SideBar from './components/SideBar.vue';
/* eslint-disable */
import EditButton from './components/EditButton.vue';
import EditModal from './components/EditProductModal.vue';
import { AgGridVue } from 'ag-grid-vue';
// event bus imported
import { EventBus } from './EventBus';

export default {
  name: 'App',
  components: {
    AgGridVue,
    SideBar,
    EditButton,
    EditModal
  },
  data() {
    return {
      // declare table data
      rowData: [], // row data is just declared because data will be imported from Fake Store API
      columnDefs: [
        { field: 'id', headerName: 'ID', hide: true },
        { field: 'title', headerName: 'Product Title', width: 350, minWidth: 200 },
        { field: 'price', headerName: 'Price', width: 150, minWidth: 100 },
        { field: 'category', headerName: 'Category', width: 150, minWidth: 100 },
        { field: 'image', headerName: 'Image', width: 350, minWidth: 200 },
        {
          // column that render cell with default command (user declare)
          field: 'action',
          headerName: 'Action',
          cellRenderer: 'EditButton',
          width: 100,
          minWidth: 80
        }
      ],
      // initialize variable as null/false at initial point
      gridApi: null,
      showModal: false,
      selectedProduct: null,
    };
  },
  // on mounted check for any emit is occurring from child component
  mounted() {
    EventBus.$on('edit-product', this.handleEditProduct);
  },
  // on created products are fetched from Fake Store API and saved in rowData
  created() {
    fetch("https://fakestoreapi.com/products")
      .then((response) => response.json())
      .then((data) => {
        this.rowData = data;
      });
  },
  methods: {
    // writing methods for each function
    addProductToGrid(product) {
      this.rowData = [...this.rowData, product];
    },
    handleEditProduct(product) {
      this.selectedProduct = { ...product }; // Clone the product object to avoid direct mutations
      this.showModal = true;
    },
    saveProduct(updatedProduct) {
      const index = this.rowData.findIndex(p => p.id === updatedProduct.id);
      if (index !== -1) {
        this.rowData[index] = updatedProduct;
        const newProducts = this.rowData;
        this.rowData = [...newProducts];
        console.log(this.gridApi);
        console.log(this.rowData);
      }
      this.closeModal();
    },
    closeModal() {
      this.showModal = false;
      this.selectedProduct = null;
    },
    onGridReady(params) {
      this.gridApi = params.api;
    },
  }
}
</script>
