<template lang="pug">
.mx-auto.mt-8.w-auto.flex.flex-col.gap-4
  n-space(vertical)
    label.font-medium Введите нуклеотиды:
    n-input.uppercase(
      v-model:value="rawInput",
      maxlength="100",
      show-count,
      placeholder="A, C, T или G",
      autofocus,
      clearable
    )

  .complementary-group.flex.flex-col
    label.font-medium Комплиментарный фрагмент(кф):
    output {{ complementary }}

  .heat-group
    label.font-medium Температура плавления кф:
    br
    output {{ heat }}
</template>

<script setup lang="ts">
import { ref, computed, watch } from "vue";
import { NSpace, NInput } from "naive-ui";

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