<template>
  <div class="max-w-7xl mx-auto mt-10">
    <h1 class="text-4xl text-center bg-gray-400/50 pb-12 pt-4 rounded-t-xl">
      Prices Place
    </h1>
    <section class="rounded-xl border overflow-auto shadow-2xl -mt-6">
      <table class="min-w-full divide-y divide-gray-300">
        <thead class="bg-gray-50">
          <tr>
            <th
              scope="col"
              class="py-3.5 pl-4 pr-3 text-left text-lg font-semibold text-gray-900 sm:pl-6"
            >
              #
            </th>
            <th
              scope="col"
              class="px-3 py-3.5 text-left text-lg font-semibold text-gray-900"
            >
              Name
            </th>
            <th
              scope="col"
              class="px-3 py-3.5 text-right text-lg font-semibold text-gray-900"
            >
              Price
            </th>
            <th
              scope="col"
              class="px-3 py-3.5 text-center text-lg font-semibold text-gray-900"
            >
              24h %
            </th>
            <th
              scope="col"
              class="px-3 py-3.5 text-center text-lg font-semibold text-gray-900"
            >
              High
            </th>
            <th
              scope="col"
              class="px-3 py-3.5 text-center text-lg font-semibold text-gray-900"
            >
              Low
            </th>
          </tr>
        </thead>
        <tbody class="bg-white">
          <tr
            v-for="(crypto, index) in cryptoResponse"
            :key="crypto.id"
            class="cursor-pointer hover:bg-gray-100"
            @click="openNewBuy(crypto)"
          >
            <td class="py-4 pl-4 pr-3 text-base text-gray-500 sm:pl-6">
              {{ index + 1 }}
            </td>
            <td class="whitespace-nowrap px-3 py-4 text-lg w-min">
              <div class="flex flex-row items-center space-x-2">
                <img class="h-8 w-8" :src="crypto.image" :alt="crypto.name" />
                <h1 class="text-black">{{ crypto.name }}</h1>
                <h1 class="font-semibold uppercase text-gray-500/70">
                  {{ crypto.symbol }}
                </h1>
              </div>
            </td>
            <td class="whitespace-nowrap px-3 py-4 text-lg text-right">
              $ {{ formatPrice(crypto.current_price) }}
            </td>
            <td class="whitespace-nowrap px-3 py-4 text-lg text-center">
              <h1
                :class="
                  crypto.price_change_percentage_24h >= 0
                    ? 'text-green-600'
                    : 'text-red-600'
                "
              >
                {{ crypto.price_change_percentage_24h }}
              </h1>
            </td>
            <td class="whitespace-nowrap text-center px-3 py-4 text-lg">
              $ {{ formatPrice(crypto.high_24h) }}
            </td>
            <td class="whitespace-nowrap text-center px-3 py-4 text-lg">
              $ {{ formatPrice(crypto.low_24h) }}
            </td>
          </tr>
        </tbody>
      </table>
    </section>

    <TransitionRoot appear :show="openBuy" as="template">
      <Dialog as="div" @close="closeNewBuy()" class="relative z-10">
        <TransitionChild
          as="template"
          enter="duration-300 ease-out"
          enter-from="opacity-0"
          enter-to="opacity-100"
          leave="duration-200 ease-in"
          leave-from="opacity-100"
          leave-to="opacity-0"
        >
          <div class="fixed inset-0 bg-black bg-opacity-25" />
        </TransitionChild>

        <div class="fixed inset-0 overflow-y-auto">
          <div
            class="flex min-h-full items-center justify-center p-4 text-center"
          >
            <TransitionChild
              as="template"
              enter="duration-300 ease-out"
              enter-from="opacity-0 scale-95"
              enter-to="opacity-100 scale-100"
              leave="duration-200 ease-in"
              leave-from="opacity-100 scale-100"
              leave-to="opacity-0 scale-95"
            >
              <DialogPanel
                class="w-full max-w-lg transform overflow-hidden rounded-2xl bg-white p-8 text-left align-middle shadow-xl transition-all space-y-10"
              >
                <div class="flex flex-row justify-between">
                  <DialogTitle
                    as="h3"
                    class="text-2xl font-medium leading-6 text-gray-900"
                  >
                    Exchange
                  </DialogTitle>
                  <XIcon
                    class="h-6 w-6 cursor-pointer hover:text-yellow-600"
                    @click="closeNewBuy()"
                  />
                </div>
                <div class="mt-2">
                  <div class="w-full h-16 bg-gray-200 rounded-xl"></div>
                  <div class="relative h-8 -my-2">
                    <ArrowSmDownIcon
                      class="h-8 w-8 mx-auto bg-gray-200 rounded-xl border border-white absolute inset-0 hover:text-yellow-600 cursor-pointer"
                    />
                  </div>
                  <div class="w-full h-16 bg-gray-200 rounded-xl z-0"></div>
                </div>

                <div class="mt-4">
                  <button
                    type="button"
                    class="inline-flex justify-center rounded-md border border-transparent bg-yellow-300 px-4 py-2 text-sm font-medium hover:bg-yellow-400"
                  >
                    Comprar
                  </button>
                </div>
              </DialogPanel>
            </TransitionChild>
          </div>
        </div>
      </Dialog>
    </TransitionRoot>
  </div>
</template>

<script setup>
import { ref } from "@vue/reactivity";
import axios from "axios";
import newBuy from "./Modals/newBuy.vue";
import {
  TransitionRoot,
  TransitionChild,
  Dialog,
  DialogPanel,
  DialogTitle,
} from "@headlessui/vue";
import { ArrowSmDownIcon, XIcon } from "@heroicons/vue/solid";

const selectedCrypto = ref({});
const cryptoResponse = ref({});
const currency = "usd";
const openBuy = ref(false);

const formatPrice = (value) => {
  let val = (value / 1).toFixed(2).replace(".", ",");
  return val.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ".");
};

function closeNewBuy() {
  openBuy.value = false;
}

function openNewBuy(crypto) {
  selectedCrypto.value = crypto;
  openBuy.value = true;
}

const fetchCryptos = async () => {
  await axios
    .get(
      `https://api.coingecko.com/api/v3/coins/markets?vs_currency=${currency}&order=market_cap_desc&per_page=100&page=1&sparkline=false`
    )
    .then((res) => (cryptoResponse.value = res.data));
};
console.log(openNewBuy);
fetchCryptos();
</script>
