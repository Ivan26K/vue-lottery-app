<template>
  <div class="mb-3">
    <label for="search" class="form-label">Пошук за іменем</label>
    <input
        id="search"
        v-model="search"
        type="text"
        class="form-control"
        placeholder="Введіть ім'я"
        @input="$emit('update:search', search)"
    />
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue';

const search = ref('');
defineEmits(['update:search']);
</script>