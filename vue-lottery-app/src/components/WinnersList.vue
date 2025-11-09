<template>
  <div class="card mb-4">
    <div class="card-header d-flex justify-content-between align-items-center">
      Winners
      <CustomButton
          label="New winner"
          :disabled="winners.length >= 3 || participants.length === 0"
          @click="$emit('add-winner')"
      />
    </div>
    <div class="card-body">
      <ul class="list-group list-group-flush">
        <WinnerItem
            v-for="(winner, index) in winners"
            :key="index"
            :winner="winner"
            :index="index"
            @remove="$emit('remove-winner', $event)"
        />
      </ul>
      <p v-if="winners.length === 0" class="text-muted">No winners yet.</p>
    </div>
  </div>
</template>

<script setup lang="ts">
import CustomButton from './CustomButton.vue';
import WinnerItem from './WinnerItem.vue';
import type { User } from '../types.ts';

defineProps<{
  winners: User[];
  participants: User[];
}>();
defineEmits(['add-winner', 'remove-winner']);
</script>