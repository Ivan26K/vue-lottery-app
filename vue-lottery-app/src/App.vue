<template>
  <div class="container mt-5">
    <h1 class="mb-4">Lottery App</h1>

    <WinnersList :winners="winners" :participants="participants" @add-winner="selectWinner" @remove-winner="removeWinner" />

    <RegisterForm ref="registerForm" :external-error="externalError" @submit="handleAddParticipant" />

    <SearchBar @update:search="searchQuery = $event" />

    <ParticipantsTable
        :participants="sortedParticipants"
        :sort-key="sortKey"
        :sort-order="sortOrder"
        @sort="handleSort"
        @edit="openEditModal"
        @delete="openDeleteModal"
    />

    <!-- Модал для редагування -->
    <Modal v-if="showEditModal" title="Редагувати дані учасника" @close="closeEditModal" @keyup.esc="closeEditModal">
      <template #body>
        <RegisterForm :initial-user="editingUser" :external-error="externalError" is-edit @submit="handleUpdateParticipant" />
      </template>
    </Modal>

    <!-- Модал для видалення -->
    <Modal v-if="showDeleteModal" title="Підтвердження видалення" @close="closeDeleteModal" @keyup.esc="closeDeleteModal">
      <template #body>
        <p v-if="deletingUser">Ви дійсно бажаєте видалити учасника "{{ deletingUser.name }}", "{{ deletingUser.email }}"?</p>
      </template>
      <template #footer>
        <CustomButton label="Так" @click="confirmDelete" />
        <CustomButton label="Ні" @click="closeDeleteModal" />
      </template>
    </Modal>
  </div>
</template>

<script setup lang="ts">
import { ref, watch, onMounted, computed } from 'vue';
import { v4 as uuidv4 } from 'uuid';
import WinnersList from './components/WinnersList.vue';
import RegisterForm from './components/RegisterForm.vue';
import ParticipantsTable from './components/ParticipantsTable.vue';
import SearchBar from './components/SearchBar.vue';
import Modal from './components/Modal.vue';
import CustomButton from './components/CustomButton.vue';

interface User {
  id: string;
  name: string;
  dob: string;
  email: string;
  phone: string;
}

const participants = ref<User[]>([]);
const winners = ref<User[]>([]);
const externalError = ref('');
const searchQuery = ref('');
const sortKey = ref<'name' | 'dob'>('name');
const sortOrder = ref<1 | -1>(1);
const showEditModal = ref(false);
const showDeleteModal = ref(false);
const editingUser = ref<User | null>(null);
const deletingUser = ref<User | null>(null);

const filteredParticipants = computed(() => {
  return participants.value.filter(p => p.name.toLowerCase().includes(searchQuery.value.toLowerCase()));
});

const sortedParticipants = computed(() => {
  return [...filteredParticipants.value].sort((a, b) => {
    if (sortKey.value === 'dob') {
      return sortOrder.value * (new Date(a.dob).getTime() - new Date(b.dob).getTime());
    }
    return sortOrder.value * a.name.localeCompare(b.name);
  });
});

onMounted(() => {
  const stored = localStorage.getItem('participants');
  if (stored) {
    participants.value = JSON.parse(stored);
  }
});

watch(participants, (newVal) => {
  localStorage.setItem('participants', JSON.stringify(newVal));
}, { deep: true });

function handleAddParticipant(user: Omit<User, 'id'>) {
  if (participants.value.some(p => p.email === user.email)) {
    externalError.value = 'Учасник з такою електронною поштою вже існує.';
    return;
  }
  externalError.value = '';
  participants.value.push({ id: uuidv4(), ...user });
}

function openEditModal(user: User) {
  editingUser.value = { ...user };
  showEditModal.value = true;
}

function closeEditModal() {
  showEditModal.value = false;
  editingUser.value = null;
  externalError.value = '';
}

function handleUpdateParticipant(updatedUser: User) {
  if (updatedUser.email !== editingUser.value?.email && participants.value.some(p => p.email === updatedUser.email)) {
    externalError.value = 'Учасник з такою електронною поштою вже існує.';
    return;
  }
  externalError.value = '';
  const index = participants.value.findIndex(p => p.id === updatedUser.id);
  if (index !== -1) {
    participants.value[index] = updatedUser;
  }
  closeEditModal();
}

function openDeleteModal(user: User) {
  deletingUser.value = user;
  showDeleteModal.value = true;
}

function closeDeleteModal() {
  showDeleteModal.value = false;
  deletingUser.value = null;
}

function confirmDelete() {
  if (deletingUser.value) {
    participants.value = participants.value.filter(p => p.id !== deletingUser.value?.id);
    winners.value = winners.value.filter(w => w.id !== deletingUser.value?.id);
  }
  closeDeleteModal();
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

function handleSort(key: 'name' | 'dob') {
  if (sortKey.value === key) {
    sortOrder.value = (sortOrder.value * -1) as 1 | -1;
  } else {
    sortKey.value = key;
    sortOrder.value = 1;
  }
}
</script>