<template>
  <div class="fixed inset-0 flex items-center justify-center bg-black bg-opacity-50" v-if="show" @click.stop="hideDialog">
    <div @click.stop class="bg-white rounded-lg p-8 w-80 relative">
      <button class="absolute top-2 right-2 text-gray-500 hover:text-gray-700" @click.stop="hideDialog">
        <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"></path>
        </svg>
      </button>
      <slot>
        <img :src="postUser.avatar" alt="User avatar" class="w-32 h-32 rounded-full mx-auto mb-4" />
        <div class="text-center font-semibold text-lg mb-2">{{ postUser.first_name }}</div>
        <div class="text-center text-sm mb-4">{{ postUser.email }}</div>
        <input type="text" v-model="postUser.phone_number" placeholder="Номер телефона" class="block w-full border-2 border-gray-300 rounded-md py-2 px-4 mb-2" />
        <input type="text" v-model="postUser.address" placeholder="Адрес проживания" class="block w-full border-2 border-gray-300 rounded-md py-2 px-4 mb-2" />
        <button @click="updateUser(postUser)" class="block w-full bg-blue-500 text-white rounded-md py-2 px-4">Подтвердить</button>
      </slot>
    </div>
  </div>
</template>

<script setup>
import { defineProps, defineEmits } from 'vue';
import axios from 'axios';

const props = defineProps({
  show: {
    type: Boolean,
    default: false
  },
  postUser: {
    type: Object,
  }
});

const emit = defineEmits(['update:show']);

const hideDialog = () => {
  emit('update:show', false);
};

const updateUser = async (postUser) => {
  try {
    await axios.put(`https://reqres.in/api/users/${postUser.id}`, {
      phone_number: postUser.phone_number,
      address: postUser.address
    });
    alert('User updated successfully');
  } catch (e) {
    console.error('Error updating user:', e);
  }
};
</script>