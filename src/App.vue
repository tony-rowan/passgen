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

    return password.value = passwordValue;
  }

  generatePassword();
</script>

<template>
  <h1>PassGen</h1>
  <p>Warning! Does not use cyrptographic random source, yet!</p>
  <h2>Your Password</h2>

  <pre> {{ password }} </pre>

  <button @click="generatePassword">Regenerate</button>

  <h3>Options</h3>
  <div>
    <input type="checkbox" id="include-symbols" v-model="includeSymbols" />
    <label for="include-symbols">Inlucde Symbols</label>
  </div>

  <div>
    <input type="checkbox" id="include-numbers" v-model="includeNumbers" />
    <label for="include-numbers">Use Numbers</label>
  </div>

  <div>
    <input type="checkbox" id="extra-long" v-model="extraLong" />
    <label for="extra-long">Extra Long</label>
  </div>
</template>

<style>
@import './assets/base.css';
</style>
