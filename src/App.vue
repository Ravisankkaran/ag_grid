<template>
  <div id="app">
    <div class="container">
      <!-- side-bar fro adding products -->
      <div class="sidebar-container"><side-bar @add-product="addProductToGrid" /></div>
      <!-- ag-grid tables defining all theames and basic styles -->
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
//added comment for error that occurs due to a component that has be registred but not used anywhere
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
      rowData: [],//row data is just declared bcz datas will be imported from fakestore api 
      columnDefs: [
        { field: 'id', headerName: 'ID', hide: true },
        { field: 'title', headerName: 'Product Title' },
        { field: 'price', headerName: 'Price' },
        { field: 'category', headerName: 'Category' },
        { field: 'image', headerName: 'Image' },
        {
          // column that render cell with default command(user declare)
          field: 'action',
          headerName: 'Action',
          cellRenderer: 'EditButton'
        }
        
      ],
      // initialize variable as null/false at initial point
      gridApi:null,
      showModal: false,
      selectedProduct: null,
    };
  },
  // on mounted check for any emit is occuring from child component
  mounted(){
    EventBus.$on('edit-product', this.handleEditProduct);
  },
  // on created products are fetched from fakestore api and saved in rowdata
  created() {
    fetch("https://fakestoreapi.com/products")
      .then((response) => response.json())
      .then((data) => {
        this.rowData = data;
      });
  },
  methods: {
    // writing methods for each functions 
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
        this.rowData[index]=updatedProduct
        const newProducts = this.rowData
        this.rowData = [...newProducts]
        console.log(this.gridApi)
        // this.rowData.push(updatedProduct)
        // this.$set(this.rowData, index, updatedProduct);
        console.log(this.rowData)
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
