<template>
  <div id="app">
    <div class="container">
      <!-- side bar container -->
      <div class="sidebar-container">
        <side-bar @add-product="addProductToGrid" />
      </div>
      <!-- Ag-grid TABLE -->
      <ag-grid-vue
      
        class="ag-theme-alpine"
        style="height: 500px;"
        :columnDefs="columnDefs"
        :rowData="rowData"
        :gridOptions="gridOptions"
        @grid-ready="onGridReady"
      >
      </ag-grid-vue>
      <!-- modal-view -->
      <EditModal
        :product="selectedProduct"
        :showModal="showModal"
        @save-product="saveProduct"
        @close="closeModal"
      />
    </div>
  </div>
</template>

<script>
// import components
import SideBar from './components/SideBar.vue';
/* eslint-disable */
import EditButton from './components/EditButton.vue';
import EditModal from './components/EditProductModal.vue';
// import ag-grid and Event bus
import { AgGridVue } from 'ag-grid-vue';
import { EventBus } from './EventBus';

export default {
  name: 'App',
  // components
  components: {
    AgGridVue,
    SideBar,
    EditButton,
    EditModal
  },
  data() {
    return {
      // initilize row and column data
      rowData: [],
      columnDefs: [
        { field: 'id', headerName: 'ID', hide: true },
        { field: 'title', headerName: 'Product Title', width: 350, minWidth: 200, cellClass: 'center-text', wrapText: true,tooltipField: 'title'  },
        { field: 'image', headerName: 'Image', width: 150, cellRenderer: params => `<img src="${params.value}" style="width: 100px; height: 100px; padding: 10px;" />` },
        { field: 'price', headerName: 'Price', width: 150, minWidth: 100, cellClass: 'center-text', cellRenderer: params => `$ ${params.value}` },
        { field: 'category', headerName: 'Category', width: 200, minWidth: 100, cellClass: 'center-text' },
        { field: 'rating.rate', headerName: 'Rating', width: 150, minWidth: 100, cellClass: 'center-text', cellRenderer: this.starRatingRenderer },
        { field: 'rating.count', headerName: 'Available Quantity', width: 150, minWidth: 100, cellClass: 'center-text', cellRenderer: params => `${params.value} Nos` },
        { field: 'rating.count', headerName: 'Status', width: 150, minWidth: 100, cellClass: 'center-status', cellRenderer: this.statusRenderer },
        { field: 'action', headerName: 'Action', cellRenderer: 'EditButton', cellClass: 'center-text', width: 100, minWidth: 80 }
      ],
      gridOptions: {
        rowHeight: 100
      },
      // initialize variable at initial state
      gridApi: null,
      showModal: false,
      selectedProduct: null,
    };
  },
  mounted() {
    // recive data from child component through event-bus
    EventBus.$on('edit-product', this.handleEditProduct);
  },
  created() {
    // fetch row data from fake store api
    fetch("https://fakestoreapi.com/products")
      .then((response) => response.json())
      .then((data) => {
        this.rowData = data;
      });
  },
  methods: {
    // declare methods
    // 1.rating
    starRatingRenderer(params) {
      const rating = params.value;
      const maxStars = 5;
      let stars = '';

      for (let i = 0; i < maxStars; i++) {
        stars += `<i class="bi ${i < rating ? 'bi-star-fill' : 'bi-star'}"></i>`;
      }

      return stars;
    },
    // 2.remainder
    statusRenderer(params) {
  const count = params.value;
  const statusClass = count > 150 ? 'status-available' : 'status-out-of-stock';
  const statusText = count > 150 ? 'Available' : 'Out of Stock';

  return `<div class="${statusClass}">${statusText}</div>`;
},
// 3.add product
    addProductToGrid(product) {
      this.rowData = [...this.rowData, product];
    },
    // 3.edit button
    handleEditProduct(product) {
      this.selectedProduct = { ...product };
      this.showModal = true;
    },
    // 4.save product
    saveProduct(updatedProduct) {
      const index = this.rowData.findIndex(p => p.id === updatedProduct.id);
      if (index !== -1) {
        this.rowData[index] = updatedProduct;
        this.gridApi.setRowData(this.rowData); // Refresh grid data
      }
      this.closeModal();
    },
    // 5.close modal
    closeModal() {
      this.showModal = false;
    },
    onGridReady(params) {
      this.gridApi = params.api;
    }
  }
}
</script>
