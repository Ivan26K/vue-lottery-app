<template>
  <div class="card mb-4">
    <div class="card-header">Register Form</div>
    <div class="card-body">
      <form @submit.prevent="handleSubmit">
        <p v-if="externalError" class="text-danger">{{ externalError }}</p>
        <CustomInput
            id="name"
            v-model="form.name"
            label="Name"
            type="text"
            placeholder="Enter user name"
            :required="true"
            :is-invalid="!!errors.name"
            :error-message="errors.name"
            @submit="handleSubmit"
        />
        <CustomInput
            id="dob"
            v-model="form.dob"
            label="Date of Birth"
            type="date"
            placeholder="mm/dd/yyyy"
            :required="true"
            :is-invalid="!!errors.dob"
            :error-message="errors.dob"
            @submit="handleSubmit"
        />
        <CustomInput
            id="email"
            v-model="form.email"
            label="Email"
            type="email"
            placeholder="Enter email"
            :required="true"
            :is-invalid="!!errors.email"
            :error-message="errors.email"
            @submit="handleSubmit"
        />
        <CustomInput
            id="phone"
            v-model="form.phone"
            label="Phone number"
            type="tel"
            placeholder="Enter Phone number"
            :required="true"
            :is-invalid="!!errors.phone"
            :error-message="errors.phone"
            @submit="handleSubmit"
        />
        <CustomButton :label="isEdit ? 'Оновити дані' : 'Save'" type="submit" />
      </form>
    </div>
  </div>
</template>

<script setup lang="ts">
import { reactive, watch } from 'vue';
import CustomInput from './CustomInput.vue';
import CustomButton from './CustomButton.vue';
import type { User } from '../types.ts';

const props = defineProps<{
  initialUser?: User;
  externalError?: string;
  isEdit?: boolean;
}>();
const emit = defineEmits(['submit']);

const form = reactive<Partial<User>>({
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

watch(() => props.initialUser, (newVal) => {
  if (newVal) {
    Object.assign(form, newVal);
  }
}, { immediate: true });

function validate(): boolean {
  let valid = true;

  errors.name = form.name?.trim() ? '' : 'This value is required.';
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
  if (!form.email?.trim()) {
    errors.email = 'This value is required.';
    valid = false;
  } else if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(form.email)) {
    errors.email = 'Invalid email format';
    valid = false;
  }

  errors.phone = '';
  if (!form.phone?.trim()) {
    errors.phone = 'This value is required.';
    valid = false;
  } else if (!/^\+?[\d\s()-]{7,15}$/.test(form.phone)) {
    errors.phone = 'Invalid phone number format';
    valid = false;
  }

  return valid;
}

function handleSubmit() {
  if (!validate()) return;
  emit('submit', { ...form } as User);
  if (!props.isEdit) {
    form.name = '';
    form.dob = '';
    form.email = '';
    form.phone = '';
    errors.name = '';
    errors.dob = '';
    errors.email = '';
    errors.phone = '';
  }
}
</script>