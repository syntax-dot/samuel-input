<template>
  <div class="avatar-wrapper" :class="{ 'avatar-wrapper--active': isActive }">
    <img
      :src="src ?? avatarDefault"
      :class="['base-avatar', `base-avatar--${size}`]"
      alt="Avatar"
    />
  </div>
</template>

<script setup lang="ts">
import avatarDefault from '../../../assets/avatar-default.png';

interface BaseAvatarProps {
  src?: string;
  isActive?: boolean;
  size?: 'default' | 'sm' | 'lg';
}

withDefaults(defineProps<BaseAvatarProps>(), {
  size: 'default',
});
</script>

<style scoped>
.base-avatar {
  border-radius: 50%;
  object-fit: cover;
}

.base-avatar--default {
  width: 80px;
  height: 80px;
}

.base-avatar--sm {
  width: 40px;
  height: 40px;
}

.base-avatar--lg {
  width: 120px;
  height: 120px;
}

.avatar-wrapper {
  width: fit-content;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 50%;
  border: 1px solid var(--color-primary);
  padding: 3px;
}
</style>
