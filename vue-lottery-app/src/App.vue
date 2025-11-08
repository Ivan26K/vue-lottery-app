<!-- src/App.vue -->
<template>
  <div class="container mt-5">
    <!-- Блок переможців -->
    <div class="card mb-4">
      <div class="card-header d-flex justify-content-between align-items-center">
        Winners
        <button
            class="btn btn-primary"
            :disabled="winners.length >= 3 || participants.length === 0"
            @click="selectWinner"
        >
          New winner
        </button>
      </div>
      <div class="card-body">
        <ul class="list-group list-group-flush">
          <li v-for="(winner, index) in winners" :key="index" class="list-group-item d-flex justify-content-between align-items-center">
            {{ winner.name }}
            <button type="button" class="btn-close" @click="removeWinner(index)"></button>
          </li>
        </ul>
      </div>
    </div>

    <!-- Блок форми реєстрації -->
    <div class="card mb-4">
      <div class="card-header">Register Form</div>
      <div class="card-body">
        <form @submit.prevent="addParticipant">
          <div class="mb-3">
            <label for="name" class="form-label">Name</label>
            <input
                id="name"
                v-model="form.name"
                type="text"
                class="form-control"
                :class="{ 'is-invalid': errors.name }"
                placeholder="Enter user name"
                required
            />
            <div v-if="errors.name" class="invalid-feedback">{{ errors.name }}</div>
          </div>
          <div class="mb-3">
            <label for="dob" class="form-label">Date of Birth</label>
            <input
                id="dob"
                v-model="form.dob"
                type="date"
                class="form-control"
                :class="{ 'is-invalid': errors.dob }"
                required
            />
            <div v-if="errors.dob" class="invalid-feedback">{{ errors.dob }}</div>
          </div>
          <div class="mb-3">
            <label for="email" class="form-label">Email</label>
            <input
                id="email"
                v-model="form.email"
                type="email"
                class="form-control"
                :class="{ 'is-invalid': errors.email }"
                placeholder="Enter email"
                required
            />
            <div v-if="errors.email" class="invalid-feedback">{{ errors.email }}</div>
          </div>
          <div class="mb-3">
            <label for="phone" class="form-label">Phone number</label>
            <input
                id="phone"
                v-model="form.phone"
                type="tel"
                class="form-control"
                :class="{ 'is-invalid': errors.phone }"
                placeholder="Enter Phone number"
                required
            />
            <div v-if="errors.phone" class="invalid-feedback">{{ errors.phone }}</div>
          </div>
          <button type="submit" class="btn btn-primary">Save</button>
        </form>
      </div>
    </div>

    <!-- Блок таблиці учасників -->
    <div class="card">
      <div class="card-header">Participants</div>
      <div class="card-body">
        <table class="table">
          <thead>
          <tr>
            <th scope="col">#</th>
            <th scope="col">Name</th>
            <th scope="col">Date of Birth</th>
            <th scope="col">Email</th>
            <th scope="col">Phone number</th>
          </tr>
          </thead>
          <tbody>
          <tr v-for="(participant, index) in participants" :key="index">
            <th scope="row">{{ index + 1 }}</th>
            <td>{{ participant.name }}</td>
            <td>{{ formatDate(participant.dob) }}</td>
            <td>{{ participant.email }}</td>
            <td>{{ participant.phone }}</td>
          </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { reactive, ref } from 'vue';

interface User {
  name: string;
  dob: string;
  email: string;
  phone: string;
}

const participants = ref<User[]>([]);
const winners = ref<User[]>([]);

const form = reactive<User>({
  name: '',
  dob: '',
  email: '',
  phone: '',
});

const errors = reactive<Record<string, string>>({
  name: '',
  dob: '',
  email: '',
  phone: '',
});

function validate(): boolean {
  let valid = true;

  errors.name = form.name.trim() ? '' : 'This value is required.';
  if (errors.name) valid = false;

  errors.dob = '';
  if (!form.dob) {
    errors.dob = 'This value is required.';
    valid = false;
  } else {
    const dobDate = new Date(form.dob);
    const now = new Date();
    if (dobDate > now) {
      errors.dob = 'Date of Birth cannot be in the future';
      valid = false;
    }
  }

  errors.email = '';
  if (!form.email.trim()) {
    errors.email = 'This value is required.';
    valid = false;
  } else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(form.email)) {
    errors.email = 'Invalid email format';
    valid = false;
  }

  errors.phone = '';
  if (!form.phone.trim()) {
    errors.phone = 'This value is required.';
    valid = false;
  } else if (!/^\+?[\d\s()-]{7,15}$/.test(form.phone)) {
    errors.phone = 'Invalid phone number format';
    valid = false;
  }

  return valid;
}

function addParticipant() {
  if (!validate()) return;

  participants.value.push({ ...form });

  form.name = '';
  form.dob = '';
  form.email = '';
  form.phone = '';

  errors.name = '';
  errors.dob = '';
  errors.email = '';
  errors.phone = '';
}

function selectWinner() {
  if (winners.value.length >= 3 || participants.value.length === 0) return;

  const randomIndex = Math.floor(Math.random() * participants.value.length);
  const winner = participants.value[randomIndex];
  if (winner) {
    winners.value.push(winner);
  }
}

function removeWinner(index: number) {
  winners.value.splice(index, 1);
}

function formatDate(date: string): string {
  const [year, month, day] = date.split('-');
  return `${day}/${month}/${year}`;
}
</script>

<style scoped>
.card {
  background-color: #f8f9fa;
}
.list-group-item {
  background-color: #f8f9fa;
  border: none;
}
</style>