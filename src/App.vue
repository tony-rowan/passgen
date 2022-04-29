<script setup lang="ts">
import { ref, watch } from "vue";
import capitalize from "lodash.capitalize";
import sample from "lodash.sample";
import sampleSize from "lodash.samplesize";

import dictionary from "./services/dictionary";

const SECONDS_IN_YEAR = 60 * 60 * 24 * 356;
const SECONDS_IN_DAY = 60 * 60 * 24;

const password = ref("");
const timeToGuessRandom = ref(0);
const timeToGuessDictionary = ref(0);
const extraLong = ref(false);

watch(extraLong, generatePassword);
watch(extraLong, calculateTimeToGuessRandom);
watch(extraLong, calculateTimeToGuessDictionary);

function generatePassword() {
  let length = extraLong.value ? 8 : 4;
  let words = sampleSize(dictionary, length);

  var passwordValue = words.map((w) => capitalize(w)).join("");

  return (password.value = passwordValue);
}

function calculateTimeToGuessRandom() {
  // let's be generous and assume the hacker knows it's alpha
  let alphabetSize = 24 + 24 + 10
  // but they don't know that it's words
  let length = password.value.length;

  let timeInSeconds = (alphabetSize ** length) / 100000000;

  return (timeToGuessRandom.value = timeInSeconds);
}

function calculateTimeToGuessDictionary() {
  // here we assume they know the password comes from here
  let dictionarySize = dictionary.length
  // and let's assume they know the length, for simplicity
  let length = extraLong.value ? 8 : 4;

  let timeInSeconds = (dictionarySize ** length) / 100000000;

  return (timeToGuessDictionary.value = timeInSeconds);
}

function humanize(duration: number) {
  function format(d: number, u: string) {
    return `${d.toPrecision(4)} ${u}`
  }

  if (duration > SECONDS_IN_YEAR) return format(duration / SECONDS_IN_YEAR, "years");
  if (duration > SECONDS_IN_DAY) return format(duration / SECONDS_IN_DAY, "days");

  return format(duration, "seconds");
}

generatePassword();
calculateTimeToGuessRandom();
calculateTimeToGuessDictionary();
</script>

<template>
  <h1 class="text-3xl text-center mb-4">PassGen</h1>
  <p class="text-center mb-8">
    ⚠️ Warning! Does not use cyrptographic random source! ⚠️
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

  <div class="flex gap-x-6 px-6 py-4 rounded-md bg-slate-800 w-max mx-auto mb-4">
    <div class="flex gap-x-1">
      <input type="checkbox" id="extra-long" v-model="extraLong" />
      <label for="extra-long">Extra Long</label>
    </div>
  </div>


  <div class="px-6 py-4 rounded-md bg-slate-800 w-max mx-auto text-center">
    <div class="text-xl mb-2">
      Time to Guess Password
    </div>

    <div class="flex gap-x-4">
      <div>
        <div class="text-lg mb-2">
          Random Guessing
        </div>
        <div class="text-sm">
          {{ humanize(timeToGuessRandom) }}
        </div>
      </div>

      <div>
        <div class="text-lg mb-2">
          Dictionary Attack
        </div>
        <div class="text-sm">
          {{ humanize(timeToGuessDictionary) }}
        </div>
      </div>
    </div>
  </div>
</template>
