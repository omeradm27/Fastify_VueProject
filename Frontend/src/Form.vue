<template>
  <div class="container">
    <div class="card">
      <div class="card-body">
        <h1 class="card-title mb-4">{{ formTitle }}</h1>
        <form @submit.prevent="submitForm">
          <div class="mb-3">
            <label for="name" class="form-label">Name:</label>
            <input type="text" id="name" class="form-control" v-model="product.name" required>
          </div>

          <div class="mb-3">
            <label for="picture" class="form-label">Picture:</label>
            <div class="input-group">
              <input type="file" id="picture" class="form-control" @change="handleFileInput">
              <label class="input-group-text" for="picture">Choose File</label>
            </div>
            <div v-if="product.pictureSrc" class="mt-2">
              <img :src="product.pictureSrc" alt="Selected Image" class="img-thumbnail" style="max-width: 100px;">
            </div>
          </div>
          <div class="d-flex justify-content-between">
            <div>
              <button type="submit" class="btn btn-primary">
                <i class="fas fa-save"></i> {{ formSubmitButtonText }}
              </button>
            </div>
            <div>
              <button class="btn btn-secondary">
                <i class="fas fa-arrow-left"></i> <router-link to="/" class="link-button">Back to List</router-link>
              </button>
            </div>
          </div>




        </form>

      </div>
    </div>
  </div>
</template>
  
<script>
import axios from 'axios';

export default {
  data() {
    return {
      formTitle: 'Add Product',
      formSubmitButtonText: 'Add',
      product: {
        name: '',
        picture: null,
        pictureSrc: '',
        created_at: '',
      },
    };
  },
  mounted() {
    if (this.$route.query.editMode) {
      this.formTitle = 'Edit Product';
      this.formSubmitButtonText = 'Update';
      this.product = {
        id: this.$route.query.id,
        name: this.$route.query.name,
        picture: this.$route.query.picture,
        pictureSrc: this.$route.query.picture,
        created_at: this.$route.query.createdAt,
      };
    }
  },
  methods: {
    submitForm() {
      if (this.formSubmitButtonText === 'Add') {
        this.addProduct();
      } else if (this.formSubmitButtonText === 'Update') {
        this.updateProduct();
      }
    },
    addProduct() {
      axios
        .post('http://localhost:3000/products/add', {
          name: this.product.name,
          picture: this.product.pictureSrc,
        })
        .then((res) => {
          console.log('Product added:', res.data);
          this.resetForm();
        })
        .catch((err) => {
          console.error('Error adding product:', err);
        });
    },
    updateProduct() {
      axios
        .put(`http://localhost:3000/products/update/?id=${this.product.id}`, {
          name: this.product.name,
          picture: this.product.pictureSrc,
        })
        .then((res) => {
          console.log('Product updated:', res.data);
          this.resetForm();
        })
        .catch((err) => {
          console.error('Error updating product:', err);
        });
    },
    resetForm() {
      this.product = {
        name: '',
        picture: null,
        pictureSrc: '',
        created_at: '',
      };
      this.$router.push({
        path: '/',
      });
    },
    handleFileInput(event) {
      const file = event.target.files[0];
      this.product.picture = file;
      console.log('file', file);

      const reader = new FileReader();
      reader.onload = () => {
        this.product.pictureSrc = reader.result;
      };
      reader.readAsDataURL(file);
    },
  },
};
</script>
  
<style scoped>
.container {
  max-width: 500px;
  margin: 0 auto;
}

.card {
  border: none;
  box-shadow: 0px 2px 6px rgba(0, 0, 0, 0.1);
}

.card-body {
  padding: 2rem;
}
.link-button {
  text-decoration: none;
  color: white;
}

</style>
  