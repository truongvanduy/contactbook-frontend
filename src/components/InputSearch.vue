<script setup>
defineProps({
  modelValue: {
    type: String,
    default: '',
  },
});
const emit = defineEmits(['submit', 'update:modelValue']);

function updateModelValue(e) {
  emit('update:modelValue', e.target.value);
}

function submit() {
  emit('submit');
}
</script>

<template>
  <div class="input-group">
    <input
      type="text"
      name="q"
      class="form-control"
      placeholder="Nhập thông tin cần tìm"
      :value="modelValue"
      @input="updateModelValue"
      @keyup.enter="submit" />
    <div class="input-group-append">
      <button
        class="btn btn-outline-secondary"
        type="button"
        @click="submit">
        <i class="fas fa-search"></i>
        Tìm kiếm
      </button>
    </div>
  </div>
</template>

<style lang="scss" scoped></style>
