<template>
  <div class=" min-h-screen relative  pb-5 ">
    <div class="border-b  border-gray-300 lg:mx-auto   ">
      <div
        class=" border-r border-gray-300 w-16 md:w-16 lg:w-full lg:container  lg:border-r-0 lg:mx-auto "
      >
        <img
          class="w-16 h-16 object-cover  "
          src="@/assets/limelogo.png"
          alt=""
        />
      </div>
    </div>
    <div class=" lg:border-b relative  lg:-mt-px ">
      <div class="lg:flex lg:container lg:mx-auto relative  lg:w-full">
        <div
          class="lg:flex lg:justify-between  lg:items-center lg:w-full lg:border-l  border-gray-300   "
        >
          <div class=" border-b lg:border-b-0       ">
            <div
              class="  lg:pl-2   lg:w-40    font-semibold  tracking-wide p-4 lg:p-0 "
            >
              TEST CHALLENGE
            </div>
          </div>

          <div class=" md:flex md:w-full  lg:justify-end ">
            <div
              class="px-2 lg:px-0 mt-5 lg:mt-0 md:w-9/12 md lg:w-dropLength lg:border-gray-300 lg:border-t     lg:border-l"
            >
              <input
                type="text"
                placeholder="Search Products"
                class="w-full lg:shadow-none lg:border-r lg:border-gray-300  lg:py-3     shadow-sm py-3 px-6 border lg:border-0   outline-white rounded-sm text-sm leading-7"
                v-model="search"
              />
            </div>

            <div class="px-2 mt-5 lg:mt-0 flex  w-full lg:w-auto ">
              <FilterProducts
                :categories="filteredCategories"
                v-model:filterBy="filterResults"
                @updateSortResults="sortResults = $event"
                ref="filterProducts"
                v-click-outside="handleFocusOut"
              />
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="lg:container lg:mx-auto lg:mt-10">
      <Loading v-if="loading" />
      <!-- class="px-2 mt-5 flex md:inline-flex md:gap-5  flex-col md:flex-row flex-wrap md:justify-between  " -->
      <div
        class=" px-2 mt-5 grid grid-cols-1 gap-5 md:grid-cols-2 md:gap-2 lg:grid-cols-4 lg:gap-5  mb-10  "
        v-else-if="displayedProducts && displayedProducts.length"
      >
        <Products
          v-for="items in displayedProducts"
          :key="items"
          :items="items"
          class=" "
        />
      </div>
      <div
        v-else
        class="px-2 mt-10 text-sm text-center flex items-center justify-center "
      >
        <p>
          You have no item that matches <br />
          the searched product
        </p>
      </div>
      <div
        v-if="displayedProducts && displayedProducts.length"
        class="flex items-center px-2 mt-5 lg:absolute fixed md:absolute bottom-0 flex-1 lg:mb-5  md:mb-5 container lg:mx-auto bg-white "
      >
        <button
          @click="previousPage"
          :class="{ 'opacity-50 cursor-not-allowed': currentPage === 1 }"
          :disabled="currentPage === 1"
          class="w-full border flex border-r-0  justify-center items-center border-gray-300 h-14 text-xs"
        >
          <svg class="w-3 h-3 ml-2 transform rotate-90" viewBox="0 0 20 10">
            <path
              fill="none"
              stroke="hsl(200, 10%, 12%)"
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="1.5"
              d="M1.58.95L10 9.05m8.42-8.1L10 9.05"
            ></path>
          </svg>
        </button>
        <div
          class="w-32 border h-14 border-gray-300 flex items-center justify-center"
        >
          {{ currentPage }}
        </div>
        <button
          @click="nextPage"
          :class="{ 'opacity-50 cursor-not-allowed': currentPage === 3 }"
          :disabled="currentPage === 3"
          class="w-full border border-l-0 flex  justify-center items-center border-gray-300 h-14 text-xs"
        >
          <svg class="w-3 h-3 ml-2 transform  -rotate-90" viewBox="0 0 20 10">
            <path
              fill="none"
              stroke="hsl(200, 10%, 12%)"
              stroke-linecap="round"
              stroke-linejoin="round"
              stroke-width="1.5"
              d="M1.58.95L10 9.05m8.42-8.1L10 9.05"
            ></path>
          </svg>
        </button>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import Products from "../components/Products.vue";
import Loading from "../components/Loading.vue";
import FilterProducts from "../components/FilterProducts.vue";
import vClickOutside from "click-outside-vue3";

export default {
  name: "Home",

  components: {
    Products,
    Loading,
    FilterProducts,
  },

  data() {
    return {
      currentPage: 1,
      lastPage: 3,
      products: [],
      loading: false,
      search: "",
      filteredCategories: [],
      filterResults: [],
      sortResults: "recent",
    };
  },

  directives: {
    clickOutside: vClickOutside.directive,
  },

  computed: {
    displayedProducts() {
      let products = this.products;
      if (this.search) {
        products = products.filter((product) =>
          product.name.toLowerCase().match(this.search.toLowerCase())
        );
      }
      if (this.filterResults.length > 0) {
        products = products.filter((product) =>
          this.filterResults.includes(product.catname)
        );
      }
      if (this.sortResults === "recent" || this.sortResults === "") {
        return products;
      } else if (this.sortResults === "least") {
        return products.sort(
          (product1, product2) => product1.price - product2.price
        );
      } else {
        return products.sort(
          (product1, product2) => product2.price - product1.price
        );
      }
    },
  },
  methods: {
    async nextPage() {
      this.currentPage += 1;
      await this.getAllProducts();
    },
    async previousPage() {
      this.currentPage -= 1;
      await this.getAllProducts();
    },
    async getAllProducts() {
      this.loading = true;
      let response = await axios.get(
        `https://trayvonnorthern.com/Edgewood-API/public/api/products?page=${this.currentPage}`
      );
      this.products = response.data.data;
      [...this.products].forEach((product) => {
        if (!this.filteredCategories.includes(product.catname)) {
          this.filteredCategories.push(product.catname);
        }
      });
      this.loading = false;
    },

    handleFocusOut() {
      this.$refs.filterProducts.changeType("");
    },
  },
  async created() {
    await this.getAllProducts();
  },
};
</script>
