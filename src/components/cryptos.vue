<template>
  <div class="max-w-7xl mx-auto my-10">
    <h1
      class="text-4xl text-center bg-gray-200/50 border shadow-xl pb-12 pt-4 rounded-t-xl"
    >
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
                class="w-full max-w-xl transform rounded-2xl bg-white p-8 text-left align-middle shadow-xl transition-all space-y-10"
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
                  <div
                    class="w-full h-20 px-4 bg-gray-100 rounded-xl flex flex-row items-center justify-between"
                  >
                    <!-- Comienzo de lista selectora de primer crypto -->
                    <Listbox v-model="selectedFromCrypto">
                      <div class="relative w-2/5">
                        <ListboxButton
                          class="relative w-full rounded-lg bg-white shadow-md text-left cursor-pointer sm:text-sm"
                        >
                          <div
                            v-if="!selectedFromCrypto.id"
                            class="py-3 px-2 text-center hover:bg-yellow-500/60 rounded-lg"
                          >
                            <span class="uppercase font-semibold"
                              >Seleccione una crypto</span
                            >
                          </div>
                          <div
                            class="flex flex-row items-center justify-between py-1 px-3"
                            v-else
                          >
                            <img
                              :src="selectedFromCrypto.image"
                              alt="cryptoImage"
                              class="h-10"
                            />
                            <span
                              class="block truncate uppercase font-semibold text-2xl -ml-4"
                              >{{ selectedFromCrypto.symbol }}</span
                            >
                            <SelectorIcon class="h-5 w-5 text-gray-400 ml-6" />
                          </div>
                        </ListboxButton>

                        <transition
                          leave-active-class="transition duration-100 ease-in"
                          leave-from-class="opacity-100"
                          leave-to-class="opacity-0"
                        >
                          <ListboxOptions
                            class="absolute mt-1 max-h-60 w-full overflow-auto rounded-md bg-white py-1 text-base shadow-lg sm:text-sm z-10"
                          >
                            <ListboxOption
                              v-slot="{ active, selected }"
                              v-for="crypto in cryptoResponse.filter(
                                (c) => c.id !== selectedToCrypto.id
                              )"
                              :key="crypto.id"
                              :value="crypto"
                              as="template"
                              class="cursor-pointer"
                            >
                              <li
                                :class="[
                                  active
                                    ? 'bg-amber-100 text-amber-900'
                                    : 'text-gray-900',
                                  'relative select-none py-2 px-2',
                                ]"
                              >
                                <div
                                  class="flex flex-row items-center space-x-2"
                                >
                                  <img
                                    :src="crypto.image"
                                    alt="cryptoImage"
                                    class="h-8"
                                  />
                                  <div class="leading-none">
                                    <span
                                      :class="[
                                        selected
                                          ? 'font-bold'
                                          : 'font-semibold',
                                        'block truncate',
                                      ]"
                                      class="uppercase text-base"
                                      >{{ crypto.symbol }}</span
                                    >
                                    <span class="text-xs text-gray-400">{{
                                      crypto.name
                                    }}</span>
                                  </div>
                                </div>
                                <span
                                  v-if="selected"
                                  class="absolute inset-y-0 right-0 flex items-center pl-3 text-amber-600"
                                >
                                  <CheckIcon
                                    class="h-5 w-5"
                                    aria-hidden="true"
                                  />
                                </span>
                              </li>
                            </ListboxOption>
                          </ListboxOptions>
                        </transition>
                      </div>
                    </Listbox>
                    <!-- Fin de lista selectora de primer crypto -->
                    <input
                      v-if="selectedToCrypto.id && selectedFromCrypto.id"
                      v-model="fromQuantity"
                      class="bg-transparent text-right text-3xl w-1/3 outline-none"
                      required
                      type="number"
                      placeholder="0"
                      inputmode="decimal"
                      min="1"
                      step=".0001"
                      @change="convertFromPrices()"
                    />
                  </div>
                  <div class="relative h-8 -my-2">
                    <SwitchVerticalIcon
                      class="h-10 w-10 mx-auto bg-gray-100 p-1 rounded-xl border border-white absolute inset-0 hover:text-yellow-600 cursor-pointer"
                      @click="switchCrypto()"
                    />
                  </div>
                  <div
                    class="w-full h-20 px-4 bg-gray-100 rounded-xl flex flex-row items-center justify-between"
                  >
                    <!-- Comienzo de lista selectora de segunda crypto -->
                    <Listbox v-model="selectedToCrypto">
                      <div class="relative w-2/5">
                        <ListboxButton
                          class="relative w-full rounded-lg bg-white shadow-md text-left cursor-pointer sm:text-sm"
                        >
                          <div
                            v-if="!selectedToCrypto.id"
                            class="py-3 px-2 text-center hover:bg-yellow-500/60 rounded-lg"
                          >
                            <span class="uppercase font-semibold"
                              >Seleccione una crypto</span
                            >
                          </div>
                          <div
                            class="flex flex-row items-center justify-between py-1 px-3"
                            v-else
                          >
                            <img
                              :src="selectedToCrypto.image"
                              alt="cryptoImage"
                              class="h-10"
                            />
                            <span
                              class="block truncate uppercase font-semibold text-2xl -ml-4"
                              >{{ selectedToCrypto.symbol }}</span
                            >
                            <SelectorIcon class="h-5 w-5 text-gray-400 ml-6" />
                          </div>
                        </ListboxButton>

                        <transition
                          leave-active-class="transition duration-100 ease-in"
                          leave-from-class="opacity-100"
                          leave-to-class="opacity-0"
                        >
                          <ListboxOptions
                            class="absolute mt-1 max-h-60 w-full overflow-auto rounded-md bg-white py-1 text-base shadow-lg sm:text-sm z-10"
                          >
                            <ListboxOption
                              v-slot="{ active, selected }"
                              v-for="crypto2 in cryptoResponse.filter(
                                (c) => c.id !== selectedFromCrypto.id
                              )"
                              :key="crypto2.id"
                              :value="crypto2"
                              as="template"
                              class="cursor-pointer"
                            >
                              <li
                                v-if="
                                  selectedFromCrypto.symbol !== crypto2.symbol
                                "
                                :class="[
                                  active
                                    ? 'bg-amber-100 text-amber-900'
                                    : 'text-gray-900',
                                  'relative select-none py-2 px-2',
                                ]"
                              >
                                <div
                                  class="flex flex-row items-center space-x-2"
                                >
                                  <img
                                    :src="crypto2.image"
                                    alt="cryptoImage"
                                    class="h-8"
                                  />
                                  <div class="leading-none">
                                    <span
                                      :class="[
                                        selected
                                          ? 'font-bold'
                                          : 'font-semibold',
                                        'block truncate',
                                      ]"
                                      class="uppercase text-base"
                                      >{{ crypto2.symbol }}</span
                                    >
                                    <span class="text-xs text-gray-400">{{
                                      crypto2.name
                                    }}</span>
                                  </div>
                                </div>
                                <span
                                  v-if="selected"
                                  class="absolute inset-y-0 right-0 flex items-center pl-3 text-amber-600"
                                >
                                  <CheckIcon
                                    class="h-5 w-5"
                                    aria-hidden="true"
                                  />
                                </span>
                              </li>
                            </ListboxOption>
                          </ListboxOptions>
                        </transition>
                      </div>
                    </Listbox>
                    <!-- Fin de lista selectora de segunda crypto -->
                    <input
                      v-if="selectedToCrypto.id && selectedFromCrypto.id"
                      v-model="toQuantity"
                      class="bg-transparent text-right text-3xl w-1/3 outline-none"
                      required
                      type="number"
                      placeholder="0"
                      inputmode="decimal"
                      min="1"
                      @change="convertToPrices()"
                    />
                  </div>
                </div>

                <div class="mt-4">
                  <div
                    class="inline-flex justify-center rounded-md bg-yellow-300 px-4 py-2 text-sm font-medium hover:bg-yellow-400 cursor-pointer"
                  >
                    Comprar
                  </div>
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
  Listbox,
  ListboxLabel,
  ListboxButton,
  ListboxOptions,
  ListboxOption,
} from "@headlessui/vue";
import { SwitchVerticalIcon, XIcon } from "@heroicons/vue/outline";
import { CheckIcon, SelectorIcon } from "@heroicons/vue/solid";

const selectedFromCrypto = ref({});
const fromQuantity = ref(null);
const toQuantity = ref(null);
const selectedToCrypto = ref({});
const cryptoResponse = ref({});
const currency = "usd";
const openBuy = ref(false);

const formatPrice = (value) => {
  let val = (value / 1).toFixed(2).replace(".", ",");
  return val.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ".");
};

function closeNewBuy() {
  fromQuantity.value = null;
  toQuantity.value = null;
  openBuy.value = false;
}

function openNewBuy(crypto) {
  selectedFromCrypto.value = crypto;
  openBuy.value = true;
}

function switchCrypto() {
  let aux = selectedFromCrypto.value;
  let aux2 = fromQuantity.value;
  selectedFromCrypto.value = selectedToCrypto.value;
  fromQuantity.value = toQuantity.value;
  selectedToCrypto.value = aux;
  toQuantity.value = aux2;
}

const fetchCryptos = async () => {
  await axios
    .get(
      `https://api.coingecko.com/api/v3/coins/markets?vs_currency=${currency}&order=market_cap_desc&per_page=100&page=1&sparkline=false`
    )
    .then((res) => (cryptoResponse.value = res.data));
};

function convertFromPrices() {
  const relation =
    selectedFromCrypto.value.current_price /
    selectedToCrypto.value.current_price;

  toQuantity.value = fromQuantity.value * relation;
}

function convertToPrices() {
  const relation =
    selectedToCrypto.value.current_price /
    selectedFromCrypto.value.current_price;

  fromQuantity.value = toQuantity.value * relation;
}

fetchCryptos();
</script>
