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
        :gridOptions="gridOptions"
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
        { field: 'title', headerName: 'Product Title', width: 350, minWidth: 200,cellClass: 'center-text',wrapText: true },
        { field: 'image', headerName: 'Image', width:150,
         cellRenderer: params => `<img src="${params.value}" style="width: 100px; height: 100px ;     padding: 10px;" />` 
        },
        { field: 'price', headerName: 'Price', width: 150, minWidth: 100 ,cellClass: 'center-text',  cellRenderer: params => `$ ${params.value}`},
        { field: 'category', headerName: 'Category', width: 200, minWidth: 100,cellClass: 'center-text' },
        { field: 'rating.rate', headerName: 'Rating', width: 150, minWidth: 100 , cellClass: 'center-text', cellRenderer: this.starRatingRenderer},
        { field: 'rating.count', headerName: 'Avilable Quantity', width: 150, minWidth: 100 , cellClass: 'center-text',cellRenderer: params => ` ${params.value} Nos`},
        
        { 
          field: 'rating.count',
          headerName: 'Status',
          width: 150,
          minWidth: 100,
          cellClass: 'center-status',
          cellRenderer: this.statusRenderer
        },
        {
          // column that render cell with default command (user declare)
          field: 'action',
          headerName: 'Action',
          cellRenderer: 'EditButton',
          cellClass: 'center-text',
          width: 100,
          minWidth: 80
        }
      ],
      gridOptions: {
        rowHeight: 100  // Set row height to 100px
      },
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
    starRatingRenderer(params) {
      const rating = params.value;
      const maxStars = 5; // Maximum number of stars
      let stars = '';

      for (let i = 0; i < maxStars; i++) {
  stars += `<i class="bi ${i < rating ? 'bi-star-fill' : 'bi-star'}"></i>`;
}


      return stars;
    },
    statusRenderer(params) {
      const count = params.value;
    const eDiv = document.createElement('div');
    
    if (count > 150) {
      eDiv.innerHTML = 'Available';
      eDiv.style.backgroundColor = 'lightgreen';
      eDiv.style.color = 'green';
      // eDiv.style.border = '2px solid lightgreen';
      eDiv.style.padding = '5px';
      eDiv.style.borderRadius ='10px';
      eDiv.style.textAlign = 'center';
      eDiv.style.fontWeight= 'bold';
      eDiv.style.padding='0 10px';
    } else {
      eDiv.innerHTML = 'Out of Stock';
      eDiv.style.backgroundColor = 'lightcoral';
      eDiv.style.color = 'red';
      eDiv.style.borderRadius ='10px';
      eDiv.style.fontWeight= 'bold';
      // eDiv.style.border = '2px solid lightcoral';
      eDiv.style.padding = '5px';
      eDiv.style.textAlign = 'center';
      eDiv.style.padding=' 0 10px';
    }

    return eDiv;
    },
    // writing methods for each function
    addProductToGrid(product) {
      this.rowData = [...this.rowData, product];
    },
    handleEditProduct(product) {
      this.selectedProduct = { ...product }; // Clone the product object to avoid direct mutations
      console.log(product)
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

