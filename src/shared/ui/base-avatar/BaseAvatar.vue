<template>
  <div>
    <img :src="src" class="avatar" alt="Avatar" />
    <div class="avatar-border"></div>
  </div>
</template>

<script setup lang="ts">
interface BaseAvatarProps {
  src: string;
  size: 'default' | 'sm' | 'lg';
  isActive?: boolean;
}

withDefaults(defineProps<BaseAvatarProps>(), {
  size: 'default',
});
</script>

<style scoped></style>
