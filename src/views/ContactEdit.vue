<script setup>
import ContactForm from '../components/ContactForm.vue';
import { ref, onMounted } from 'vue';
import contactService from '../services/contact.service';
import { useRoute, useRouter } from 'vue-router';

const props = defineProps({
  id: {
    type: String,
    required: true,
  },
});

const contact = ref(null);
const message = ref('');
const router = useRouter();
const route = useRoute();

async function getContact(id) {
  try {
    contact.value = await contactService.get(id);
  } catch (error) {
    console.log(error);

    router.push({
      name: 'notfound',
      params: {
        pathMatch: route.path.split('/').slice(1),
      },
      query: route.query,
      hash: route.hash,
    });
  }
}

async function updateContact(data) {
  try {
    await contactService.update(contact.value._id, data);
    message.value = 'Liên hệ được cập nhật thành công.';
  } catch (error) {
    console.log(error);
  }
}

async function deleteContact() {
  if (!confirm('Bạn muốn xóa liên hệ này?')) {
    return;
  }
  try {
    await contactService.delete(contact.value._id);
    router.push({ name: 'contactbook' });
  } catch (error) {
    console.log(error);
  }
}

onMounted(() => {
  getContact(props.id);
  message.value = '';
});
</script>

<template>
  <div
    v-if="contact"
    class="page">
    <h4>Hiệu chỉnh liên hệ</h4>
    <ContactForm
      :contact="contact"
      @submit:contact="updateContact"
      @delete:contact="deleteContact" />
    <p>{{ message }}</p>
  </div>
</template>
