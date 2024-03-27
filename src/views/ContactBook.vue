<script setup>
import { useRouter } from 'vue-router';
import ContactCard from '../components/ContactCard.vue';
import ContactList from '../components/ContactList.vue';
import InputSearch from '../components/InputSearch.vue';
import ContactService from '../services/contact.service';
import { ref, computed, watch, onMounted } from 'vue';

const contacts = ref([]);
const activeIndex = ref(-1);
const router = useRouter();
const searchText = ref('');

// Chuyển các đối tượng contact thành chuỗi để tiện cho tìm kiếm.
const contactStrings = computed(() => {
  return contacts.value.map((contact) => {
    const { name, email, address, phone } = contact;
    return [name, email, address, phone].join('');
  });
});
// Trả về các contact có chứa thông tin cần tìm kiếm.
const filteredContacts = computed(() => {
  if (!searchText.value) {
    return contacts.value;
  }
  return contacts.value.filter((_contact, index) =>
    contactStrings.value[index].includes(searchText.value)
  );
});
const activeContact = computed(() => {
  if (activeIndex.value < 0) return null;
  return filteredContacts.value[activeIndex.value];
});
const filteredContactsCount = computed(() => {
  return filteredContacts.value.length;
});

async function retrieveContacts() {
  try {
    contacts.value = await ContactService.getAll();
  } catch (error) {
    console.log(error);
  }
}
function refrestList() {
  retrieveContacts();
  activeIndex.value = -1;
}
async function removeAllContacts() {
  if (confirm('Bạn muốn xóa tất cả liên hệ ?'))
    try {
      await ContactService.deleteAll();
      refrestList();
    } catch (error) {
      console.log(error);
    }
}

function goToAddContact() {
  router.push({ name: 'contact.add' });
}

onMounted(() => {
  refrestList();
});
</script>
<template>
  <div class="page row">
    <div class="col-md-10">
      <InputSearch v-model="searchText" />
    </div>
    <div class="mt-3 col-md-6">
      <h4>
        Danh bạ
        <i class="fas fa-address-book"></i>
      </h4>
      <ContactList
        v-if="filteredContactsCount > 0"
        :contacts="filteredContacts"
        v-model:active-index="activeIndex" />
      <p v-else>Không có liên hệ nào</p>

      <div class="mt-3 row justify-content-around align-items-center">
        <button
          class="btn btn-sm btn-primary"
          @click="refrestList()">
          <i class="fas fa-redo"></i>
          Làm mới
        </button>

        <button
          class="btn btn-sm btn-success"
          @click="goToAddContact">
          <i class="fas fa-plus"></i>
          Thêm mới
        </button>

        <button
          class="btn btn-sm btn-danger"
          @click="removeAllContacts">
          <i class="fas fa-trash"></i>
          Xóa tất cả
        </button>
      </div>
    </div>
    <div class="mt-3 col-md-6">
      <div v-if="activeContact">
        <h4>
          Chi tiết Liên hệ
          <i class="fas fa-address-card"></i>
        </h4>
        <ContactCard :contact="activeContact" />
      </div>
    </div>
  </div>
</template>

<style lang="scss">
.page {
  text-align: left;
  max-width: 750px;
}
</style>
