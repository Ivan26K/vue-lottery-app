<template>
  <div class="mb-3">
    <label :for="id" class="form-label">{{ label }}</label>
    <input
        :id="id"
        :value="modelValue"
        :type="type"
        class="form-control"
        :class="{ 'is-invalid': isInvalid }"
        :placeholder="placeholder"
        :required="required"
        @input="$emit('update:modelValue', ($event.target as HTMLInputElement).value)"
        @keyup.enter="$emit('submit')"
    />
    <div v-if="errorMessage" class="invalid-feedback">{{ errorMessage }}</div>
  </div>
</template>

<script setup lang="ts">
defineProps<{
  id: string;
  modelValue: string;
  label: string;
  type: string;
  placeholder: string;
  required?: boolean;
  isInvalid?: boolean;
  errorMessage?: string;
}>();
defineEmits(['update:modelValue', 'submit']);
</script>