<template>
  <div id="app" class="max-w-3xl mx-auto p-6 mt-8 bg-gray-200 rounded-lg shadow-lg  ">
    <h1 class="text-3xl font-bold text-center mb-8">Product Wishlist</h1>

    <form @submit.prevent="addProduct" class="mb-8">
      <h2 class="text-xl font-semibold mb-4">Add Product</h2>
      <div>
        <label for="name" class="block text-sm font-medium text-gray-700">Product Name</label>
        <input v-model="newProduct.name" type="text" placeholder="Product Name" required id="name"
          class="w-full px-4 py-2 border rounded-md  mb-3" />

        <label for="price" class="block text-sm font-medium text-gray-700">Price</label>
        <input v-model="newProduct.price" type="number" placeholder="Price" required id="price"
          class="w-full px-4 py-2 border rounded-md  mb-3" />
        <label for="imageURL" class="block text-sm font-medium text-gray-700">Image URL</label>
        <input v-model="newProduct.imageURL" type="url" placeholder="Image URL" id="imageURL"
          class="w-full px-4 py-2 border rounded-md mb-3" />
        <button type="submit"
          class="w-full bg-blue-500 text-white py-2 rounded-md hover:bg-blue-600 transition duration-300">
          Add Product
        </button>
      </div>
    </form>

    <div>
      <h2 class="text-xl font-semibold mb-4">Your Wishlist</h2>
      <div v-if="products.length === 0" class="text-center text-gray-500">
        Your wishlist is empty!
      </div>
      <ul v-else class="space-y-4">
        <li v-for="product in products" :key="product.id"
          class="flex items-center bg-gray-100 p-4 rounded-lg shadow-sm">
          <div>
            <div v-if="product.imageURL" class="w-16 h-16 mr-1.5">
              <img :src="product.imageURL" alt="Product Image" class="w-full h-full object-cover rounded-md " />
            </div>
            <div v-else class="w-16 h-16 bg-gray-300 flex items-center justify-center rounded-md mr-1.5">
              <span class="text-gray-500 text-center">No Image</span>
            </div>
          </div>

          <div class="flex-grow">
            <h3 class="font-semibold">{{ product.name }}</h3>
            <p class="text-gray-700">{{ product.price }}$</p>
          </div>
          <div class="flex space-x-2">
            <button v-if="!product.purchased" @click="markAsPurchased(product.id)"
              class="px-4 py-2 bg-green-500 text-white rounded-md hover:bg-green-600 transition duration-300">
              Mark as Purchased
            </button>
            <span v-else class="px-4 py-2 bg-green-200 text-green-800 rounded-md">
              Purchased
            </span>
            <button @click="removeProduct(product.id)"
              class="px-4 py-2 bg-red-500 text-white rounded-md hover:bg-red-600 transition duration-300">
              Remove
            </button>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, watch } from 'vue';

const products = ref<{ id: number; name: string; price: number; imageURL: string; purchased: boolean }[]>([]);

const newProduct = ref({
  name: '',
  price: 0,
  imageURL: '',
});

let id = 0;

const savedProducts = localStorage.getItem('wishlist');
if (savedProducts) {
  products.value = JSON.parse(savedProducts);
  // Update the ID counter to avoid duplicate IDs
  if (products.value.length > 0) {
    id = products.value[products.value.length - 1].id + 1;
  }
}

watch(products, (newProducts) => {
  localStorage.setItem('wishlist', JSON.stringify(newProducts));
},
  { deep: true }
);

function addProduct() {
  if (newProduct.value.name && newProduct.value.price > 0) {
    products.value.push({ id: id++, ...newProduct.value, purchased: false });
    newProduct.value = {
      name: '',
      price: 0,
      imageURL: '',
    };
  }
}

function markAsPurchased(productId: number) {
  const product = products.value.find((p) => p.id === productId);
  if (product) {
    product.purchased = true;
  }
}

function removeProduct(productId: number) {
  products.value = products.value.filter((p) => p.id !== productId);
}
</script>
