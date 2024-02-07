<script setup>
import { ref, onMounted, computed } from 'vue';
import axios from 'axios';
import infoUser from '@/components/infoUser.vue';
import { ErrorMessage, Field, Form } from "vee-validate"
import * as Yup from "yup"




const posts = ref([]);
const newUser = ref({
  first_name: '',
  email: ''
});
const dialogVisible = ref(false);
const postUser = ref({});
const search = ref('');

const fetching = async () => {
  try {
    const response = await axios.get('https://reqres.in/api/users');
    posts.value = response.data.data;
  } catch (e) {
    alert('Error fetching users');
  }
}

const addUser = async () => {
  try {
    const responseNewUser = await axios.post('https://reqres.in/api/users', {
      first_name: newUser.value.first_name,
      email: newUser.value.email
    });
    posts.value.push(responseNewUser.data);
    newUser.value.first_name = '';
    newUser.value.email = '';
  } catch (e) {
    alert('Error adding user');
  }
};

const deleteUser = async (id) => {
  try {
    await axios.delete(`https://reqres.in/api/users/${id}`);
    posts.value = posts.value.filter(post => post.id !== id);
  } catch (e) {
    alert('Error deleting user');
  }
};

const showDialog = (post) => {
  dialogVisible.value = true;
  postUser.value = post;
}
const c_schema = computed(() => {
  return Yup.object().shape({
    name: Yup.string().required(),
    email: Yup.string().email().required(),
  })
})

const filteredPosts = computed(() =>
    posts.value.filter(user => user.first_name.toLowerCase().includes(search.value.toLowerCase()))
);

onMounted(fetching);
</script>

<template>
  <info-user v-model:show="dialogVisible" :postUser="postUser"></info-user>
  <div class="mx-auto max-w-lg my-8">
    <div class="my-4">
      <input class="border-2 border-cyan-500 px-4 py-2 rounded-md w-full" v-model="search" placeholder="Search..." />
    </div>
    <Form @submit="addUser" :validation-schema="c_schema" class="space-y-4">
      <div class="space-y-4">
        <label class="block">
          <span class="text-gray-700">Name:</span>
          <Field v-model="newUser.first_name" name="name" type="text" class="block w-full mt-1 border-2 border-gray-300 rounded-md shadow-sm focus:border-cyan-500 focus:ring focus:ring-cyan-500 focus:ring-opacity-50" />
          <ErrorMessage name="name" class="text-red-500 mt-1" />
        </label>
        <label class="block">
          <span class="text-gray-700">Email:</span>
          <Field v-model="newUser.email" name="email" type="text" class="block w-full mt-1 border-2 border-gray-300 rounded-md shadow-sm focus:border-cyan-500 focus:ring focus:ring-cyan-500 focus:ring-opacity-50" />
          <ErrorMessage name="email" class="text-red-500 mt-1" />
        </label>
      </div>
      <button type="submit" class="px-4 py-2 bg-cyan-500 text-white rounded-md hover:bg-cyan-600 focus:outline-none focus:bg-cyan-600 w-full">Add User</button>
    </Form>
  </div>
  <div class="mx-auto max-w-lg">
    <div class="mt-8">
      <transition-group name="fade" tag="div">
        <div v-for="user in filteredPosts" :key="user.id" class="flex items-center justify-between border-2 border-cyan-500 rounded-md p-4 mb-4">
          <img v-if="user.avatar" :src="user.avatar" alt="User avatar" class="h-16 w-16 rounded-full" />
          <img v-else src="@/components/icons/149452.png" alt="Default avatar" class="h-16 w-16 rounded-full" />
          <div class="flex flex-col">
            <div>
              <button @click="showDialog(user)" class="text-cyan-500 font-semibold hover:text-cyan-600 focus:outline-none focus:text-cyan-600">Name: {{ user.first_name }}</button>
            </div>
            <span class="text-gray-700">Email: {{ user.email }}</span>
          </div>
          <div>
            <button @click="deleteUser(user.id)" class="px-4 py-2 bg-cyan-500 text-white rounded-md hover:bg-cyan-600 focus:outline-none focus:bg-cyan-600">Delete</button>
          </div>
        </div>
      </transition-group>
    </div>
  </div>
</template>




<style>
.fade-enter-active, .fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active in <2.1.8 */ {
  opacity: 0;
}
</style>
