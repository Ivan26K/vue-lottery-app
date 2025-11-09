<template>
  <button class="btn btn-primary" :disabled="disabled" :type="type" @click="$emit('click')">
    {{ label }}
  </button>
</template>

<script setup lang="ts">
defineProps<{
  label: string;
  disabled?: boolean;
  type?: 'button' | 'submit';
}>();
defineEmits(['click']);
</script>