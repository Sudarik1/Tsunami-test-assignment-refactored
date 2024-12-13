<template>
  <div class="content">
    <h1 class="header">{{ isEdit ? 'Изменить контакт' : 'Новый контакт' }}</h1>
    <form class="form" @submit.prevent="handleSubmit">
      <label class="form__label">Имя <span class="form--asterisk-blue">*</span></label>
      <input 
      class="form__input" 
      v-model="editContact.name" 
      placeholder="Иванов Иван Иванович" 
      required 
      pattern="^[a-zA-Zа-яА-ЯёЁ\s'\-]+$"
      />
      <label class="form__label">Email <span class="form--asterisk-blue">*</span></label>
      <input class="form__input" 
      v-model="editContact.email" 
      placeholder="example@mail.com" 
      required
      pattern="^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$"
      />
      <label class="form__label">Телефон <span class="form--asterisk-blue">*</span></label>
      <input class="form__input" 
      v-model="editContact.phone" 
      placeholder="+7 999 999-99-99" 
      required
      pattern="^\+?[0-9\s\-\(\)]{7,17}$"
      />
      <button class="form__btn" type="submit">{{ isEdit ? 'ИЗМЕНИТЬ КОНТАКТ' : 'ДОБАВИТЬ КОНТАКТ' }}</button>
    </form>
  </div>
</template>
  
<script setup lang="ts">

import { ref, onMounted } from 'vue';
import { useRoute, useRouter } from 'vue-router';
import { useGlobalState } from '../stores/GlobalState';

import type { ContactInterface } from '@/types/ContactInterface.ts'

const route = useRoute();
const router = useRouter();
const globalState = useGlobalState();

const isEdit = route.params.id !== undefined || route.path === '/edit/';
const editContact = ref({ name: '', phone: '', email: '', id: ''});

onMounted(() => {
  if (isEdit) {
    const existingContact = globalState.contacts.find((contact: ContactInterface) => contact.id === String(route.params.id));
    if (existingContact) {
      editContact.value = { ...existingContact };
    }
  }
});

const handleSubmit = async () => {
  if (isEdit) {
    await globalState.updateContact(editContact.value);
  } else {
    await globalState.addContact(editContact.value);
  }
  router.push('/');
};
</script>

<style scoped lang="scss">
@import '../assets/scss/_config.scss';
@mixin alignColumn(){
  display: flex;
  flex-direction: column;
}

.content {
  @include alignColumn();

  width: 40%;
  margin: 10% 25%;

  color: #2D2A42;

  .header {
    font-size: 2.25em;
    font-weight: 700;
  }

  .form {
    @include alignColumn();

    &__label {
      font-weight: 700;
      margin: 15px 0 5px 0;
    }

    &--asterisk-blue {
      color: $primary-color;
      font-family: $secondary-font-family;
    }

    &__input {
      color: #878499;
      border: 1px solid #EEECF4;
      border-radius: 10px;

      padding: 15px;

      font-family: $secondary-font-family;
    }

    &__btn {
      align-self: start;

      width: 50%;

      font-size: 1em;
      font-weight: 550;

      margin-top: 20px;

      padding: 20px;

      border-radius: 50px;

      color: #FFFFFF;
      background-color: $primary-color;
    }
  }
}
</style>


  