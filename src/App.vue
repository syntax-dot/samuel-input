<template>
  <div class="samuel-block">
    <BaseAvatar :is-active="isFocus" />
    <HoursInput v-model="hours" @focus="isFocus = true" @blur="isFocus = false" />
  </div>
</template>

<script setup lang="ts">
import { HoursInput } from './features/hours-input';
import { ref } from 'vue';
import { BaseAvatar } from './shared/ui/base-avatar';

const hours = ref(7);
const isFocus = ref(false);
</script>

<style scoped>
.samuel-block {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
  align-items: center;
  justify-content: center;
}
</style>
