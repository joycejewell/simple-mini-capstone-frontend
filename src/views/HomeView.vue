<script>
import axios from "axios";

export default {
  data: function () {
    return {
      message: "Welcome to Vue.js!",
      products: [],
      errors: [],
      currentProduct: {},
      editProduct: {},
      newProductName: "",
      newProductPrice: "",
      newProductDescription: "",
      newProductInventory: "",
      newProductSupplierId: "",
    };
  },
  created: function () {
    this.indexProducts();
  },
  methods: {
    indexProducts: function () {
      axios.get("http://localhost:3000/products").then((response) => {
        this.products = response.data;
        console.log(response.data);
      });
    },
    showProduct: function (product) {
      console.log(product);
      this.currentProduct = product;
      this.editProduct = product;
      document.querySelector("#product-info").showModal();
    },
    updateProduct: function (productToEdit) {
      axios.patch("http://localhost:3000/products/" + productToEdit.id + ".json", productToEdit).then((response) => {
        console.log(response.data);
      });
    },
    destroyProduct: function () {
      axios.delete(`http://localhost:3000/products/${product.id}`).then((response) => {
        console.log(response.data);
        var index = this.product.indexOf(product);
        this.product.splice(index, 1);
      });
    },
    createProduct: function () {
      console.log("new product created!");
      var params = {
        name: this.newProductName,
        price: this.newProductPrice,
        description: this.newProductDescription,
        inventory: this.newProductInventory,
        supplier_id: this.newProductSupplierId,
      };

      // console.log(params);
      axios
        .post("http://localhost:3000/products.json", params)
        .then((response) => {
          console.log("Success!", response.data);
          this.products.push(response.data);
          this.newProductName = "";
          this.newProductPrice = "";
          this.newProductDescription = "";
          this.newProductInventory = "";
          this.newProductSupplierId = "";
        })
        .catch((error) => {
          console.log(error.response.data.errors);
          this.errors = error.response.data.errors;
        });
    },
  },
};
</script>

<template>
  <div class="home">
    <h1>New Product</h1>
    Name:
    <input type="text" v-model="newProductName" />
    Price:
    <input type="text" v-model="newProductPrice" />
    Description:
    <input type="text" v-model="newProductDescription" />
    Inventory:
    <input type="text" v-model="newProductInventory" />
    SupplierID:
    <input type="text" v-model="newProductSupplierId" />
    <button v-on:click="createProduct()">Create Product</button>
    <h1>{{ message }}</h1>
    <div v-for="product in products" v-bind:key="product.id">
      <button v-on:click="showProduct(product)">More Info</button>
      <p>Name: {{ product.name }}</p>
      <img v-bind:src="product.image_url" v-bind:alt="product.name" />
    </div>
    <dialog id="product-info">
      <form method="dialog">
        <h1>Product Info:</h1>
        <p>Name: {{ currentProduct.name }}</p>
        <p>Price: {{ currentProduct.price }}</p>
        <p>Description: {{ currentProduct.description }}</p>
        <p>Inventory: {{ currentProduct.inventory }}</p>
        <p>SupplierId: {{ currentProduct.supplierId }}</p>
        <h1>Edit Product</h1>
        <p>
          Name:
          <input type="text" v-model="editProduct.name" />
        </p>
        <p>
          Price:
          <input type="text" v-model="editProduct.price" />
        </p>
        <p>
          Inventory:
          <input type="text" v-model="editProduct.inventory" />
        </p>
        <button v-on:click="updateProduct(editProduct)">Update Product</button>
        <button v-on:click="destroyProduct(currentProduct)">Delete Product</button>
        <button>Close</button>
      </form>
    </dialog>
  </div>
</template>

<style>
img {
  max-width: 500px;
}
img {
  max-width: 250px;
}
.errors {
  background-image: linear-gradient(to left, violet, indigo, blue, green, yellow, orange, red);
  background-clip: text;
  color: transparent;
}
</style>
