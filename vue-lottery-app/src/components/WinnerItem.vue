<template>
  <li class="list-group-item d-flex justify-content-between align-items-center">
    {{ winner.name }}
    <button type="button" class="btn-close" @click="$emit('remove', index)"></button>
  </li>
</template>

<script setup lang="ts">
import type { User } from '../types.ts';

defineProps<{
  winner: User;
  index: number;
}>();
defineEmits(['remove']);
</script>