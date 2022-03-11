<script setup lang="ts">
import { ref, watch } from "vue";
import capitalize from "lodash.capitalize";
import sample from "lodash.sample";
import sampleSize from "lodash.samplesize";

import dictionary from "./services/dictionary";
import symbols from "./services/symbols";
import numbers from "./services/numbers";

const password = ref("");
const includeSymbols = ref(false);
const includeNumbers = ref(false);
const extraLong = ref(false);

watch(includeSymbols, generatePassword);
watch(includeNumbers, generatePassword);
watch(extraLong, generatePassword);

function generatePassword() {
  let length = extraLong.value ? 8 : 4;
  let words = sampleSize(dictionary, length);

  var passwordValue = words.map((w) => capitalize(w)).join("");
  if (includeNumbers.value) {
    passwordValue += "-" + sampleSize(numbers, 4).join("");
  }
  if (includeSymbols.value) {
    passwordValue += "-" + sampleSize(symbols, 4).join("");
  }

  return (password.value = passwordValue);
}

generatePassword();
</script>

<template>
  <h1 class="text-3xl text-center mb-4">PassGen</h1>
  <p class="text-center mb-8">
    ⚠️ Warning! Does not use cyrptographic random source, yet! ⚠️
  </p>

  <h2 class="text-2xl text-center mb-4">Your Password</h2>

  <pre
    class="text-xl rounded-lg bg-slate-700 px-12 py-6 mb-8 w-max mx-auto whitespace-pre-line"
  >
    {{ password }}
  </pre>

  <button
    @click="generatePassword"
    class="block bg-sky-500 hover:bg-sky-700 px-6 py-2 rounded-full shadow mb-6 mx-auto"
  >
    Regenerate
  </button>

  <div class="flex gap-x-6 px-6 py-4 rounded-md bg-slate-800 w-max mx-auto">
    <div class="flex gap-x-1">
      <input type="checkbox" id="include-symbols" v-model="includeSymbols" />
      <label for="include-symbols">Inlucde Symbols</label>
    </div>

    <div class="flex gap-x-1">
      <input type="checkbox" id="include-numbers" v-model="includeNumbers" />
      <label for="include-numbers">Use Numbers</label>
    </div>

    <div class="flex gap-x-1">
      <input type="checkbox" id="extra-long" v-model="extraLong" />
      <label for="extra-long">Extra Long</label>
    </div>
  </div>
</template>
