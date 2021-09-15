<template>
  <div class="flex w-full relative">
    <button
      @click="changeType('sort')"
      class="w-full border lg:border-t-0 flex lg:w-32  justify-center items-center lg:border-b-0 border-gray-300 py-4 text-xs"
    >
      <span>
        SORT BY
      </span>
      <svg
        :class="{ ' transform  rotate-180': type === 'sort' }"
        class="w-3 h-3 ml-2"
        viewBox="0 0 20 10"
      >
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
    <button
      @click="changeType('filter')"
      class="w-full border lg:border-t-0 border-l-0 lg:border-b-0 flex lg:w-32  justify-center items-center border-gray-300 py-4 text-xs"
    >
      <span>
        FILTER BY
      </span>
      <svg
        :class="{ ' transform  rotate-180': type === 'filter' }"
        class="w-3 h-3 ml-2"
        viewBox="0 0 20 10"
      >
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
      class=" bg-white border shadow mt-12 top-1  lg:top-2  absolute z-40 w-full  lg:w-dropLength"
      v-if="type === 'sort'"
    >
      <div
        :class="sortType === 'recent' ? '  border-l-2 border-green-400 ' : ''"
        class="py-3 leading-6 pl-5  text-xs cursor-pointer"
        @click="sortType = 'recent'"
      >
        Most Recent
      </div>
      <hr />
      <div
        :class="sortType === 'least' ? '  border-l-2 border-green-400 ' : ''"
        class="py-3 leading-6 pl-5  text-xs cursor-pointer"
        @click="sortType = 'least'"
      >
        Price (Least Expensive)
      </div>
      <hr />
      <div
        :class="sortType === 'most' ? '  border-l-2 border-green-400 ' : ''"
        class="py-3 leading-6 pl-5  text-xs cursor-pointer"
        @click="sortType = 'most'"
      >
        Price (Most Expensive)
      </div>
    </div>
    <div
      class=" bg-white border shadow mt-12 top-1 lg:top-2 absolute z-40 w-full lg:w-dropLength"
      v-if="type === 'filter'"
    >
      <div
        v-for="category in categories"
        :key="category"
        @click="sortType = 'recent'"
      >
        <div
          class="py-3 leading-6 pl-5 flex items-center  text-xs cursor-pointer"
        >
          <input
            :value="category"
            v-model="computedFilterBy"
            class="mr-3 w-4 h-4"
            type="checkbox"
            name=""
            id=""
          />
          {{ category }}
        </div>
        <hr />
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    categories: {
      type: Array,
    },
    filterBy: {
      type: Array,
      default: () => [],
    },
  },
  data() {
    return {
      type: "",
      sortType: "recent",
    };
  },
  watch: {
    sortType: {
      immediate: true,
      handler(event) {
        this.$emit("updateSortResults", event);
      },
    },
  },
  computed: {
    computedFilterBy: {
      get() {
        return this.filterBy;
      },
      set(categories) {
        this.$emit("update:filterBy", categories);
      },
    },
  },

  methods: {
    changeType(type) {
      if (this.type === type) {
        this.type = "";
        return;
      }
      this.type = type;
    },
    updateCat(x) {
      let k = [];
      if (!k.includes(x.target.value)) {
        k.push(x.target.value);
      }
      this.$emit("update:filterBy", k);
      // this.$emit("update:filterBy", x.target.value);
    },
  },
};
</script>

<style></style>
