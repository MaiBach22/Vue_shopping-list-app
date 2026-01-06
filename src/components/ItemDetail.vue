<script setup>
defineProps(["item"]);

const emit = defineEmits(["completed", "delete"]);

const handleCheck = (item) => {
  emit("completed", item.id);
};

const handleDelete = (item) => {
  emit("delete", item.id);
};
</script>

<template>
  <div
    @click="handleCheck(item)"
    :class="[
      'w-full px-4 md:px-[25px] py-3 md:py-[15px] mb-2 border-2 rounded-[10px] flex justify-between items-center cursor-pointer transition-all',
      item.purchased
        ? 'border-red-200 bg-gradient-to-r from-red-50 to-orange-50'
        : 'border-slate-200 bg-white hover:border-black',
    ]"
  >
    <div class="flex items-center gap-3 md:gap-4 overflow-hidden">
      <div
        :class="[
          'w-5 h-5 md:w-6 md:h-6 rounded-full border-2 flex-shrink-0 flex items-center justify-center transition-colors',
          item.purchased
            ? 'bg-brand-red border-transparent'
            : 'border-gray-300',
        ]"
      >
        <svg
          v-if="item.purchased"
          class="w-3 h-3 md:w-4 md:h-4 text-white"
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

      <h3
        :class="[
          'text-base md:text-lg font-medium truncate pr-2',
          item.purchased ? 'line-through text-gray-400' : 'text-slate-800',
        ]"
      >
        {{ item.label }}
      </h3>
    </div>

    <button
      class="p-2 hover:bg-red-100 rounded-full transition-colors group flex-shrink-0"
      @click.stop="handleDelete(item)"
    >
      <svg
        class="w-5 h-5 text-gray-400 group-hover:text-red-600 transition-colors"
        fill="none"
        viewBox="0 0 24 24"
        stroke="currentColor"
      >
        <path
          stroke-linecap="round"
          stroke-linejoin="round"
          stroke-width="2"
          d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"
        />
      </svg>
    </button>
  </div>
</template>
