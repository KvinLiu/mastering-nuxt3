<template>
  <label
    ><input
      type="checkbox"
      :value="modelValue"
      @input="() => $emit('update:modelValue', !modelValue)"
      class="hidden"
    />
    {{ modelValue ? "Complete!" : "Mark as complete" }}
  </label>
</template>

<script setup>
defineProps({
  modelValue: {
    type: Boolean,
    default: false,
  },
});
defineEmits(["update:modelValue"]);
</script>

<style scoped></style>
