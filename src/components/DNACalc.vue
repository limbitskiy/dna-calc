<template lang="pug">
.input-group
  label Введите нуклеотиды:
  input(v-model="rawInput", style="text-transform: uppercase", maxlength="100") 
  span Осталось {{ symbolsLeft }} символов

.complementary-group
  label Комплиментарный фрагмент:
  output {{ complementary }}

.heat-group
  label Температура плавления последовательности:
  output {{ heat }}
</template>

<script setup lang="ts">
import { ref, computed, watch } from "vue";

type handleInputFunc = (a: string) => string;
type getComplementaryFunc = (a: string) => string;
type getHeatFunc = (a: string) => number;
type getOccurancesFunc = (a: string, b: string) => number;

type nucleoPairsType = {
  [a: string]: string;
};
const nucleoPairs: nucleoPairsType = {
  A: "T",
  T: "A",
  C: "G",
  G: "C",
};

const errors = [];

const input = ref("");
const rawInput = ref("");

const handleInput: handleInputFunc = (rawString) => {
  let processedInput = "";

  processedInput = rawString
    .toUpperCase()
    .split("")
    .filter((letter) => nucleoPairs[letter])
    .join("");

  rawInput.value = processedInput;
  return processedInput;
};

watch(rawInput, () => {
  input.value = handleInput(rawInput.value);
});

let symbolsLeft = computed(() => {
  return 100 - input.value.length;
});

// const checkForMaxSymbols = (e) => {
//   // console.log(e, symbolsLeft.value);

//   if (symbolsLeft.value === 0 && e) {
//     if (errors.length) return;
//     errors.push({ type: "maxCharacters" });
//     console.log(errors);
//   } else if (symbolsLeft.value > 0) {
//     errors.filter((error) => error.type !== "maxCharacters");
//   }
// };

const complementary = computed(() => getComplementary(input.value));

const heat = computed(() => getHeat(input.value));

const getComplementary: getComplementaryFunc = (inputValue) => {
  return inputValue
    .split("")
    .map((letter) => nucleoPairs[letter])
    .join("");
};

const getOccurances: getOccurancesFunc = (letter, string) => {
  const regexp = RegExp(`${letter}`, "g");
  const match = string.match(regexp);
  return match?.length || 0;
};

const getHeat: getHeatFunc = (inputValue) => {
  if (!inputValue) return 0;
  const a = getOccurances("A", inputValue);
  const t = getOccurances("T", inputValue);
  const c = getOccurances("C", inputValue);
  const g = getOccurances("G", inputValue);
  return +((64.9 + 41 * (g + c - 16.4)) / (a + t + g + c)).toFixed(2);
};
</script>