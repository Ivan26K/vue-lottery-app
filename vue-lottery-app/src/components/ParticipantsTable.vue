<template>
  <div class="card">
    <div class="card-header">Participants</div>
    <div class="card-body">
      <table class="table table-striped">
        <thead>
        <tr>
          <th scope="col">#</th>
          <th scope="col">
            Name
            <i :class="getSortIcon('name')" class="bi" @click="$emit('sort', 'name')" />
          </th>
          <th scope="col">
            Date of Birth
            <i :class="getSortIcon('dob')" class="bi" @click="$emit('sort', 'dob')" />
          </th>
          <th scope="col">Email</th>
          <th scope="col">Phone number</th>
          <th scope="col">Actions</th>
        </tr>
        </thead>
        <tbody>
        <tr v-for="(participant, index) in participants" :key="participant.id">
          <th scope="row">{{ index + 1 }}</th>
          <td>{{ participant.name }}</td>
          <td>{{ formatDate(participant.dob) }}</td>
          <td>{{ participant.email }}</td>
          <td>{{ participant.phone }}</td>
          <td>
            <CustomButton label="Редагувати дані" @click="$emit('edit', participant)" />
            <CustomButton label="Видалити учасника" @click="$emit('delete', participant)" />
          </td>
        </tr>
        <tr v-if="participants.length === 0">
          <td colspan="6" class="text-center text-muted">No participants yet.</td>
        </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script setup lang="ts">
import CustomButton from './CustomButton.vue';
import type { User } from '../types.ts';

const props = defineProps<{
  participants: User[];
  sortKey: 'name' | 'dob';
  sortOrder: 1 | -1;
}>();
defineEmits(['sort', 'edit', 'delete']);

function formatDate(date: string): string {
  const [year, month, day] = date.split('-');
  return `${day}/${month}/${year}`;
}

function getSortIcon(key: 'name' | 'dob'): string {
  if (props.sortKey !== key) return 'bi-sort-down';
  return props.sortOrder === 1 ? 'bi-sort-alpha-down' : 'bi-sort-alpha-up';
}
</script>