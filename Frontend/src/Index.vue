<template>
  <div class="container">
    <div class="card">
      <div class="card-body">
        <h1 class="card-title mb-4">Product List</h1>
        <button class="btn btn-primary mb-4" @click="goToFormPage"> <i class="fas fa-plus"></i> Add Product</button>
        <table class="table">
          <thead>
            <tr>
              <th>ID</th>
              <th>Name</th>
              <th>Picture</th>
              <th>Created At</th>
              <th>Action</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="product in products" :key="product.id">
              <td>{{ product.id }}</td>
              <td>{{ product.name }}</td>
              <td><img :src="product.picture" :alt="product.name" height="50" /></td>
              <td>{{ new Date(product.createdAt).toLocaleString('tr-TR', {
                dateStyle: 'short',
                timeStyle: 'short',
              }) }}</td>
              <td>
                <button class="btn btn-primary mr-2" @click="editProduct(product)">
                  <i class="fas fa-edit"></i> Edit
                </button>
                <button class="btn btn-danger" @click="confirmDelete(product)">
                  <i class="fas fa-trash-alt"></i> Delete
                </button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>

import axios from 'axios';

export default {
  data() {
    return {
      products: [],
      toastCount: 0
    };
  },
  mounted() {
    this.fetchProducts();
  },
  methods: {

    goToFormPage() {
      this.$router.push('/form');
    },
    fetchProducts() {
      axios.get('http://localhost:3000/products')
        .then(response => {
          this.products = response.data;
        })
        .catch(error => {
          console.log(error);
        });
    },
    confirmDelete(product) {
      if (confirm('Are you sure you want to delete this product?')) {
        this.deleteProduct(product);
      }
    },
    editProduct(product) {
      console.log("edit", product)
      this.$router.push({
        path: '/form',
        query: {
          id: product.id,
          name: product.name,
          picture: product.picture,
          createdAt: product.createdAt,
          editMode: true,
        },
      });
    },
    deleteProduct(product) {
      console.log("deleteID", product.id)
      axios.delete(`http://localhost:3000/products/delete/?id=${product.id}`)
        .then((res) => {
          this.products = this.products.filter(p => p.id !== parseInt(product.id));
          console.log(res)
        })
        .catch(error => {
          console.error('Error:', error);
        });
    },
  },
};
</script>
<style>
.card-title {
  font-size: 24px;
  margin-bottom: 20px;
  text-align: center;
}

.btn {
  /* Other button styles */
  margin-right: 10px;
  /* Adjust the value to increase or decrease the spacing */
}
</style>


