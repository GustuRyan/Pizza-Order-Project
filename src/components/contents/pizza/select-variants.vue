<template>
    <div v-if="isOpen" class="w-full h-full">
        <section class="flex justify-center items-center w-screen h-screen">
            <div class="w-[56%] h-fit flex flex-col bg-white rounded-lg shadow-lg fixed z-[65] top-[30%]">
                <div class="w-full p-5 text-xl font-bold">
                    Select Variant
                </div>
                <div class="w-full p-5 grid grid-cols-3 gap-3 border-y-2">
                    <button v-for="(topping, index) in toppings" :key="topping.id"
                        @click="addTopping(topping.id, topping.prices); toggleCheck(index)"
                        class="flex gap-3 items-center group w-fit">
                        <div class="w-10 h-10 rounded-lg border-[1px] flex justify-center items-center group-hover:bg-slate-300">
                            <img v-if="!isChecks[index]" src="/images/plus-for-cart.svg" alt="plus-logo" class="w-6 h-6">
                            <img v-else src="/images/check-for-cart.svg" alt="plus-logo" class="w-6 h-6">
                        </div>
                        <p class="text-lg font-semibold">
                            {{ topping.name }} (Rp. {{ topping.prices }})
                        </p>
                    </button>
                </div>
                <div class="w-full flex justify-end px-5 py-3">
                    {{ toppingLists }}
                    <button v-if="updates.length == 0"
                        @click="addCart(pizzaId, pizzaPrices, toppingList); $emit('change-open'); $emit('update-overall-prices'); clearCheck()"
                        class="flex items-center gap-2 px-2 py-2 w-fit rounded-md bg-[#2E74FF] hover:bg-[#2e4981]">
                        <img src="/images/cart-logo.svg" alt="cart-logo" class="w-4 h-4">
                        <span class="text-lg text-white">
                            Add to Cart
                        </span>
                    </button>
                    <button v-else
                        @click="updateCart(cart_id, toppingList); $emit('change-open'); $emit('update-overall-prices'); clearCheck(); updates.length = 0; toppingLists.length = 0"
                        class="flex items-center gap-2 px-2 py-2 w-fit rounded-md bg-[#F0BE4B] hover:bg-[#d9ac44]">
                        <img src="/images/pen_fill_white.svg" alt="cart-logo" class="w-4 h-4">
                        <span class="text-lg text-white">
                            Edit Cart
                        </span>
                    </button>
                </div>
            </div>
        </section>
        <div @click="$emit('change-open'); removeTopping(); clearCheck(); updates.length = 0; toppingLists.length = 0"
            class="w-full h-full fixed top-0 z-[60] bg-[#121212] opacity-25"></div>
    </div>
</template>

<script setup>
import { defineProps, defineEmits, reactive, toRefs, onMounted, onUnmounted, watch } from 'vue';
import { pizzaOrder } from '../../../composables/pizzaFunctions';

const props = defineProps({
    isOpen: Boolean,
    pizzaId: {
        type: Number,
        required: true
    },
    pizzaPrices: {
        type: Number,
        required: true
    },
    toppings: {
        type: Array,
        required: true
    },
    updates: {
        type: Array,
        default: () => []
    },
    toppingLists: {
        type: Array,
        default: () => []
    },
    cart_id: Number,
});

const { toppings, updates, toppingLists } = toRefs(props);
const emit = defineEmits(['change-open', 'update-overall-prices']);
const { addTopping, removeTopping, addCart, updateCart, pushList, toppingList } = pizzaOrder();

const isChecks = reactive({});

onMounted(() => {
    toppings.value.forEach((_, index) => {
        isChecks[index] = false;
    });

    pushList(toppingLists);
    
    if (updates.value.length !== 0) {
        toppings.value.forEach((topping, index) => {
            console.log('tst')
            updates.value.forEach(update => {
                if (topping.id === update.id) {
                    isChecks[index] = true;
                }
            });
        });
    }
});

const toggleCheck = (index) => {
    isChecks[index] = !isChecks[index];
};

onUnmounted(() => {
    clearCheck();
});

const clearCheck = () => {
    Object.keys(isChecks).forEach((key) => {
        isChecks[key] = false;
    });
};
</script>