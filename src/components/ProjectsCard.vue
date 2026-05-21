<template>
  <label
    class="project-thumb"
    :class="{ 'project-thumb--placeholder': project.placeholder, 'project-thumb--active': isActive }"
    @click="$emit('select')"
  >
    <span>{{ project.name }}</span>
  </label>
</template>

<script setup>
defineProps({
  project: Object,
  isActive: Boolean
})

defineEmits(['select'])
</script>