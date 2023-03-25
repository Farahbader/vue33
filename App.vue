<script setup>
import { onMounted, ref, computed } from "vue";
import makeupImage from "./assets/img/mc4gbsag_makeup650_625x300_01_June_22.webp";
import product from "./components/product.vue";
const isCartOpen = ref(false);
const products = ref([]);
onMounted(async () => {
  // products.value = [

  //   {
  //     id: 1,
  //     img: makeupImage,
  //     name: "Red lipstick",
  //     price: 16,
  //     quantity: 2,
  //   },
  //   {
  //     id: 2,
  //     img: makeupImage,
  //     name: "Red lipstick",
  //     price: 16,
  //     quantity: 1,
  //   },
  //   {
  //     id: 3,
  //     img: makeupImage,
  //     name: "Red lipstick",
  //     price: 16,
  //     quantity: 1,
  //   },
  //   {
  //     id: 4,
  //     img: makeupImage,
  //     name: "Red lipstick",
  //     price: 16,
  //     quantity: 1,
  //   },
  // ];
  products.value = await fetchproducts();
});
const numCartItems = computed(() =>
  products.value.reduce((numItems, product) => numItems + product.quantity, 0)
);
const cartTotal = computed(() =>
  products.value.reduce(
    (total, product) => total + product.price * product.quantity,
    0
  )
);

const showCart = () => {
  isCartOpen.value = true;
};
const hideCart = () => {
  isCartOpen.value = false;
};
const removeItem = ($event) => {
  const productId = $event;
  products.value = products.value.filter((product) => product.id !== productId);
};
// const removeItem = async ($event) => {
//   const productId = $event;
//   // update backend
//   const response = await fetch("http://localhost:3000/products/${productId}", {
//     method: "DELETE"
//   });
const incrementQuantity = ($event) => {
  const productId = $event;
  const productIndex = products.value.findIndex(
    (product) => product.id == productId
  );
  products.value[productIndex].quantity++;
};
const decrementQuantity = ($event) => {
  const productId = $event;
  const productIndex = products.value.findIndex(
    (product) => product.id === productId
  );

  if (products.value[productIndex].quantity === 1) removeItem(productId);
  else products.value[productIndex].quantity--;
};
const fetchproducts = async () => {
  const response = await fetch("  http://localhost:3000/products");
  const data = await response.json();
  return data;
};
</script>

<template>
  <!-- cart -->
  <button id="cart-btn" @click="showCart">
    <i class="fa-brands fa-opencart fa-xl"></i>
  </button>
  <!-- cart sidebar -->
  <div id="cart-sidebar" class="cart-sidebar" :class="{ show: isCartOpen }">
    <!-- header -->
    <div class="cart-header-padding">
      <div class="cart-header">
        <p>
          <span class="title">cart</span>
          <span class="items"
            >(<span>{{ numCartItems }}</span>
            {{ numCartItems === 1 ? "item" : "items" }})</span
          >
        </p>
        <button id="close-btn" @click="hideCart">
          <i class="fa-solid fa-xmark fa-xl"></i>
        </button>
      </div>
    </div>

    <div class="cart-products">
      <product
        v-for="product in products"
        :key="product.id"
        :product="product"
        @remove-item="removeItem"
        @increment-quantity="incrementQuantity"
        @decrement-quantity="decrementQuantity"
      />
    </div>
    <!-- footer -->
    <div class="cart-footer">
      <div class="total">
        <p>total</p>
        <p>${{ cartTotal }}</p>
      </div>
      <a href="#" class="checkout-link">checkout</a>
    </div>
  </div>
</template>

<style >
@import "@/assets/base.css";
</style>
