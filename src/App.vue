<script setup>
import { computed, ref, watch } from "vue";
import ItemDetail from "./components/ItemDetail.vue";

const items = ref([]);
const newItem = ref("");

const itemsPerPage = 4;
const currentPage = ref(1);

const hideCompleted = ref("false");

const addItem = () => {
  if (newItem.value) {
    items.value.unshift({
      id: items.value.length + 1,
      label: newItem.value,
      purchased: false,
    });
    newItem.value = "";
    currentPage.value = 1;
  }
};

const purchasedItems = computed(
  () => items.value.filter((item) => item.purchased).length
);

const filteredItems = computed(() => {
  if (hideCompleted.value) {
    return items.value.filter((item) => !item.purchased);
  }
  return items.value;
});

const onTogglePurchased = (id) => {
  const item = items.value.find((i) => i.id === id);
  if (item) {
    item.purchased = !item.purchased;
  }
};

const totalPages = computed(() => {
  return Math.ceil(filteredItems.value.length / itemsPerPage);
});

const paginatedItems = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage;
  const end = start + itemsPerPage;
  return filteredItems.value.slice(start, end);
});

const setPage = (page) => {
  if (page >= 1 && page <= totalPages.value) {
    currentPage.value = page;
  }
};

const deleteItem = (id) => {
  items.value = items.value.filter((item) => item.id !== id);
  if (currentPage.value > totalPages.value && totalPages.value > 0) {
    currentPage.value = totalPages.value;
  }
};

watch(hideCompleted, () => {
  currentPage.value = 1;
});
</script>

<template>
  <div
    class="bg-white w-full h-screen sm:h-[90vh] sm:w-[90vw] lg:w-[60vw] lg:h-[80vh] sm:rounded-[15px] sm:shadow-[5px_5px_0px_4px_black] flex justify-center mx-auto sm:mt-10 overflow-hidden transition-all"
  >
    <div
      class="p-6 md:p-10 lg:m-[40px] w-full md:w-[85%] lg:w-[70%] flex flex-col"
    >
      <header class="flex justify-between items-end gap-2">
        <h1 class="text-2xl md:text-4xl font-bold truncate">Shopping List</h1>
        <div class="flex flex-col items-end flex-shrink-0">
          <p class="font-bold text-base md:text-lg">
            {{ purchasedItems }}/{{ items.length }}
          </p>
          <span
            class="text-gray-500 text-[10px] md:text-sm uppercase tracking-wide"
            >completed</span
          >
        </div>
      </header>
      <form @submit.prevent="addItem" class="mt-8 md:mt-[50px]">
        <div
          class="w-full px-4 md:px-[25px] py-3 md:py-[15px] border-2 border-slate-200 rounded-[10px] flex justify-between items-center bg-gray-50 focus-within:border-black transition-colors"
        >
          <input
            class="border-none p-0 text-base md:text-[1.2rem] w-full bg-transparent focus:outline-none placeholder:text-gray-400"
            v-model.trim="newItem"
            type="text"
            placeholder="Add item..."
          />
          <button
            class="ml-2 px-4 md:w-[150px] h-10 border-none rounded-[10px] bg-brand-red text-white text-sm md:text-base font-semibold hover:bg-red-600 transition-colors whitespace-nowrap"
          >
            + <span class="hidden sm:inline">Add item</span>
          </button>
        </div>
      </form>
      <div class="mt-4 flex-1 md:h-[400px] overflow-hidden relative">
        <TransitionGroup
          name="list"
          tag="div"
          class="flex flex-col gap-2 relative"
        >
          <ItemDetail
            v-for="item in paginatedItems"
            :key="item.id"
            :item="item"
            @completed="onTogglePurchased"
            @delete="deleteItem"
          />
        </TransitionGroup>

        <div
          v-if="!items.length"
          class="mt-[90px] flex flex-col justify-center items-center text-center"
        >
          <img
            class="w-[90px] h-auto mb-4 opacity-50"
            alt="list-empty-icon"
            src="./assets/list-empty-64.png"
          />
          <h2 class="text-xl font-bold text-gray-700">
            Your shopping list is empty
          </h2>
          <p class="text-gray-500">
            Add items you plan to buy so you don’t forget them.
          </p>
        </div>
      </div>

      <div
        v-if="totalPages > 1"
        class="mt-4 md:mt-6 flex justify-between items-center gap-2 pb-4 sm:pb-0"
      >
        <div class="flex items-center justify-between">
          <label class="flex items-center gap-2 cursor-pointer group">
            <div class="relative">
              <input
                type="checkbox"
                v-model="hideCompleted"
                class="peer sr-only"
              />
              <div
                class="w-5 h-5 border-2 border-gray-300 rounded md:group-hover:border-brand-red peer-checked:bg-brand-red peer-checked:border-brand-red transition-all flex items-center justify-center"
              >
                <svg
                  v-if="hideCompleted"
                  class="w-3 h-3 text-white"
                  fill="none"
                  stroke="currentColor"
                  viewBox="0 0 24 24"
                >
                  <path
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    stroke-width="3"
                    d="M5 13l4 4L19 7"
                  ></path>
                </svg>
              </div>
            </div>
            <span class="text-sm font-medium text-gray-500 select-none"
              >Hide completed items</span
            >
          </label>
        </div>
        <div class="flex justify-between items-center">
          <button
            @click="setPage(currentPage - 1)"
            :disabled="currentPage === 1"
            class="p-2 disabled:opacity-30 disabled:cursor-not-allowed hover:bg-gray-100 rounded-lg transition-colors"
          >
            <svg
              xmlns="http://www.w3.org/2000/svg"
              class="w-5 h-5"
              fill="none"
              viewBox="0 0 24 24"
              stroke="currentColor"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M15 19l-7-7 7-7"
              />
            </svg>
          </button>
          <div class="flex gap-1">
            <button
              v-for="page in totalPages"
              :key="page"
              @click="setPage(page)"
              :class="[
                'w-8 h-8 rounded-lg font-bold transition-all',
                currentPage === page
                  ? 'bg-brand-red text-white shadow-sm'
                  : 'text-gray-500 hover:bg-gray-100',
              ]"
            >
              {{ page }}
            </button>
          </div>
          <button
            @click="setPage(currentPage + 1)"
            :disabled="currentPage === totalPages"
            class="p-2 disabled:opacity-30 disabled:cursor-not-allowed hover:bg-gray-100 rounded-lg transition-colors"
          >
            <svg
              xmlns="http://www.w3.org/2000/svg"
              class="w-5 h-5"
              fill="none"
              viewBox="0 0 24 24"
              stroke="currentColor"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M9 5l7 7-7 7"
              />
            </svg>
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.list-enter-active,
.list-leave-active {
  @apply transition-all duration-500 ease-in-out;
}

.list-move {
  @apply transition-transform duration-500 ease-in-out;
}

.list-enter-from {
  @apply opacity-0 -translate-y-10 scale-95;
}

.list-leave-to {
  @apply opacity-0 translate-x-20;
}

.list-leave-active {
  @apply absolute w-full;
}
</style>
