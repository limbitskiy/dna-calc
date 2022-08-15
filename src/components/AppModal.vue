<template lang="pug">
n-modal.w-96.min-h-0(
  v-model:show="showModal",
  preset="dialog",
  title="Заголовок",
  close-on-esc,
  style="width: min(100%, 600px); min-height: 300px"
)
  DNACalc
</template>

<script setup>
import { ref, onMounted } from "vue";
import { NModal } from "naive-ui";
import DNACalc from "@/components/DNACalc.vue";

const showModal = ref(false);

onMounted(() =>
  setTimeout(() => {
    showModal.value = true;
  }, 200)
);
</script>