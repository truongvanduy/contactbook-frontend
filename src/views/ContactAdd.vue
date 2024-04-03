<script setup>
import ContactForm from '../components/ContactForm.vue';
import { ref, onMounted } from 'vue';
import contactService from '../services/contact.service';
import { useRouter } from 'vue-router';

const contact = ref({
  name: '',
  email: '',
  address: '',
  favorite: false,
});
const message = ref('');
const router = useRouter();

async function addContact(data) {
  try {
    await contactService.create(contact.value);
    router.push({ name: 'contactbook' });
  } catch (error) {
    console.log(error);
  }
}

onMounted(() => {
  message.value = '';
});
</script>

<template>
  <div class="page">
    <h4>Hiệu chỉnh liên hệ</h4>
    <ContactForm
      :contact="contact"
      @submit:contact="addContact" />
    <p>{{ message }}</p>
  </div>
</template>
