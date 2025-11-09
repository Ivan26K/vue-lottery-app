<template>
  <div class="modal fade show d-block" tabindex="-1" @click.self="$emit('close')" @keyup.esc="$emit('close')">
    <div class="modal-dialog">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title">{{ title }}</h5>
          <button type="button" class="btn-close" @click="$emit('close')"></button>
        </div>
        <div class="modal-body">
          <slot name="body"></slot>
        </div>
        <div class="modal-footer">
          <slot name="footer">
            <CustomButton label="Закрити" @click="$emit('close')" />
          </slot>
        </div>
      </div>
    </div>
  </div>
  <div class="modal-backdrop fade show"></div>
</template>

<script setup lang="ts">
defineProps<{
  title: string;
}>();
defineEmits(['close']);
</script>