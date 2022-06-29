<template>
  <TransitionRoot appear :show="true" as="template">
    <Dialog as="div" @close="$emit('close')" class="relative z-10">
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
              class="w-full max-w-4xl transform overflow-hidden rounded-2xl bg-white p-6 text-left align-middle shadow-xl transition-all"
            >
              <DialogTitle
                as="h3"
                class="text-lg font-medium leading-6 text-gray-900"
              >
                Balance
              </DialogTitle>
              <div class="mt-2 border rounded-md overflow-hidden shadow-sm">
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
                        Crypto
                      </th>
                      <th
                        scope="col"
                        class="px-3 py-3.5 text-center text-lg font-semibold text-gray-900"
                      >
                        Quantity
                      </th>
                    </tr>
                  </thead>
                  <tbody class="bg-white">
                    <tr
                      v-for="(compra, index) in compras"
                      :key="compra.id"
                      class="cursor-pointer hover:bg-gray-100"
                    >
                      <td
                        class="py-4 pl-4 pr-3 text-base text-gray-500 sm:pl-6"
                      >
                        {{ index + 1 }}
                      </td>
                      <td class="whitespace-nowrap px-3 py-4 text-lg w-min">
                        <div class="flex flex-row items-center space-x-2">
                          <h1 class="text-black">{{ cryptoName(compra.crypto) }}</h1>
                        </div>
                      </td>
                      <td class="whitespace-nowrap px-3 py-4 text-lg w-min">
                        <div
                          class="flex flex-row justify-center items-center space-x-2"
                        >
                          <h1 class="text-black">{{ compra.quantity }}</h1>
                        </div>
                      </td>
                    </tr>
                  </tbody>
                </table>
              </div>

              <div class="mt-4">
                <button
                  type="button"
                  class="inline-flex justify-center rounded-md border border-transparent bg-blue-100 px-4 py-2 text-sm font-medium text-blue-900 hover:bg-blue-200 focus:outline-none focus-visible:ring-2 focus-visible:ring-blue-500 focus-visible:ring-offset-2"
                  @click="$emit('close')"
                >
                  Cerrar
                </button>
              </div>
            </DialogPanel>
          </TransitionChild>
        </div>
      </div>
    </Dialog>
  </TransitionRoot>
</template>

<script setup>
import { ref } from "vue";
import axios from "axios";
import {
  TransitionRoot,
  TransitionChild,
  Dialog,
  DialogPanel,
  DialogTitle,
} from "@headlessui/vue";

const compras = ref({});
const cryptoResponse = ref({});
const currency = "usd";

const cryptoName = (symbol) => {
  return cryptoResponse.value.find(x => x.symbol == symbol).name
  
}

const fetchCryptos = async () => {
  await axios
    .get(
      `https://api.coingecko.com/api/v3/coins/markets?vs_currency=${currency}&order=market_cap_desc&per_page=100&page=1&sparkline=false`
    )
    .then((res) => (cryptoResponse.value = res.data));
};

const fetchCompras = async () => {
  await axios
    .get(`http://localhost:8081/compras`)
    .then((res) => (compras.value = res.data));
};
fetchCompras();
fetchCryptos();
</script>
