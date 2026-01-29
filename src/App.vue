<script setup lang="ts">
import { HoursInput } from './features/hours-input';
import { ref } from 'vue';
import { BaseAvatar } from './shared/ui/base-avatar';

const hours = ref(7);
const isFocus = ref(false);
</script>

<template>
  <div class="samuel-block">
    <BaseAvatar :is-active="isFocus" />
    <HoursInput @focus="isFocus = true" @blur="isFocus = false" v-model="hours" />
  </div>
</template>

<style scoped>
.samuel-block {
  display: flex;
  flex-wrap: wrap;
  gap: 12px;
  align-items: center;
  justify-content: center;
}
</style>
