<script setup>
import { reactive, ref, watch } from "vue";

var sourceList = [
  { name: "Vitamin A", units: "mcg", dv: 900 },
  { name: "Vitamin C", units: "mg", dv: 900 },
  { name: "Vitamin D", units: "mcg", dv: 20 },
  { name: "Vitamin E", units: "mg", dv: 15 },
  { name: "Vitamin K", units: "mcg", dv: 120 },
  { name: "Thiamin (Vitamin B1)", units: "mg", dv: 12 },
  { name: "Riboflavin (Vitamin B2)", units: "mg", dv: 1.3 },
  { name: "Niacin", units: "mg", dv: 16 },
  { name: "Vitamin B6", units: "mg", dv: 1.7 },
  { name: "Folatet ", units: "mcg", dv: 400 },
  { name: "Vitamin B12", units: "mcg", dv: 2.4 },
  { name: "Pantothenic Acid", units: "mg", dv: 5 },
  { name: "Choline", units: "mg", dv: 500 },
  { name: "Calcium", units: "mg", dv: 1300 },
  { name: "Iron", units: "mg", dv: 18 },
  { name: "Phosphorus", units: "mg", dv: 1250 },
  { name: "Iodine", units: "mcg", dv: 150 },
  { name: "Magnesium", units: "mg", dv: 420 },
  { name: "Zinc", units: "mg", dv: 11 },
  { name: "Selenium", units: "mcg", dv: 55 },
  { name: "Copper", units: "mcg", dv: 0.9 },
  { name: "Macanese", units: "mg", dv: 2.3 },
  { name: "Chromium", units: "mcg", dv: 35 },
  { name: "Molybdenum", units: "mcg", dv: 45 },
  { name: "Chloride", units: "mg", dv: 2300 },
  { name: "Sodium", units: "mg", dv: 2300 },
  { name: "Potassium", units: "mg", dv: 4700 },
];
const factSheet = reactive({
  servings: 0,
  servingSize: 0,
  vitamins: [],
});

const vitamin = ref(0);
const asName = ref("");
const amount = ref(0);
const units = ref(sourceList[vitamin.value].units);
const dv = ref((amount.value / sourceList[vitamin.value].dv) * 100);
watch(
  vitamin,
  () => {
    units.value = sourceList[vitamin.value].units;
    dv.value = (amount.value / sourceList[vitamin.value].dv) * 100;
  },
  { immediate: true }
);
watch(
  amount,
  () => {
    dv.value = (amount.value / sourceList[vitamin.value].dv) * 100;
  },
  { immediate: true }
);
const error = ref("");
function addToFactSheet(sup) {
  factSheet.vitamins.push(sup);
}
function validateForm() {
  error.value = "";
  if (amount.value === 0) {
    error.value = "Please enter a valid amount";
    return false;
  }
  var sup = {};
  if (!asName.value) {
    sup = {
      name: sourceList[vitamin.value].name,
      amount: amount.value,
      units: units.value,
      dv: dv.value,
    };
  } else {
    sup = {
      name: `${sourceList[vitamin.value].name} (as ${asName.value})`,
      amount: amount.value,
      units: units.value,
      dv: dv.value,
    };
  }
  addToFactSheet(sup);
  amount.value = 0;
  vitamin.value = 0;
}
function printFactSheet() {
  print();
}
</script>

<template>
  <div class="bg-light" style="min-height: 100vh">
    <div class="container row py-4 align-items-stretch">
      <!-- Input -->
      <div class="col-md" id="input-form">
        <form
          @submit.prevent="validateForm"
          class="card card-body mb-5 p-3 w-75"
        >
          <h4 class="card-title mb-4">Add vitamin</h4>
          <div v-if="error !== ''" class="alert alert-danger mb-2" role="alert">
            <span class="font-medium">{{ error }}</span>
          </div>
          <div class="mb-2">
            <label for="countries" class="label0">Vitamin</label>
            <select v-model="vitamin" class="form-select">
              <option selected disabled value="-1">Select a vitamin</option>
              <option
                v-for="(vitamin, index) in sourceList"
                :key="index"
                :value="index"
              >
                {{ vitamin.name }}
              </option>
            </select>
          </div>
          <div class="mb-2">
            <label class="label">As</label>
            <input type="text" v-model="asName" class="form-control" />
          </div>
          <div class="mb-2">
            <label class="label">Amount</label>
            <div class="input-group">
              <input
                type="number"
                v-model="amount"
                class="form-control"
                required
              />
              <span class="input-group-text">{{ units }}</span>
            </div>
          </div>
          <div class="mb-4">
            <label for="email" class="label">Daily Value %</label>
            <input type="number" v-model="dv" class="form-control" disabled />
          </div>
          <button type="submit" class="btn btn-primary btn-block">
            Add to Fact Sheet
          </button>
        </form>
        <form class="card card-body p-3 w-75">
          <div class="mb-2">
            <label class="label">Serving Size</label>
            <input
              type="number"
              v-model="factSheet.servingSize"
              class="form-control"
              required
            />
          </div>
          <div class="mb-2">
            <label class="label">Total Servings</label>
            <input
              type="number"
              v-model="factSheet.servings"
              class="form-control"
              required
            />
          </div>
        </form>
      </div>

      <!-- Fact Sheet -->
      <div class="col-md facts h-100">
        <div class="mx-auto bg-white border border-dark">
          <div class="flex flex-col border-b-4 border-black p-2">
            <h2 class="tw-black py-1 mb-1 border-bottom thick-border">
              Supplement Fact
            </h2>
            <p class="text-dark mb-0">
              <strong>Serving Size</strong> {{ factSheet.servingSize }} capsules
            </p>
            <p class="text-dark pb-1 thick-border">
              <strong>Servings Per Container</strong> {{ factSheet.servings }}
            </p>
            <table class="table w-100">
              <thead>
                <tr class="border-bottom border-dark py-0">
                  <th class="w-50"></th>
                  <th>Amount</th>
                  <th>DV %</th>
                </tr>
              </thead>
              <tbody>
                <tr
                  v-for="(cat, index) of factSheet.vitamins"
                  :key="index"
                  class="border-bottom border-dark py-0"
                >
                  <td class="py-1">{{ cat.name }}</td>
                  <td class="py-1">{{ cat.amount }} {{ cat.units }}</td>
                  <td class="py-1">{{ cat.dv.toFixed(0) }}%</td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
        <button
          type="button"
          @click="printFactSheet"
          class="mt-5 btn btn-outline-dark w-100"
        >
          Print Fact Sheet
        </button>
      </div>
    </div>
  </div>
</template>

<style scoped>
#app {
  height: 100vh;
}
.facts {
  font-family: "Arial", sans-serif !important;
}
.thick-border {
  border-bottom: 6px solid #000 !important;
}
.tw-black {
  font-family: "Arial Black", sans-serif !important;
}
/* print mq */

@media print {
  #input-form {
    display: none;
  }
  .btn-outline-dark {
    display: none;
  }
}
</style>
