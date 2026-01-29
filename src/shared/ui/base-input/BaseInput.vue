<template>
  <label class="wrapper">
    <span v-if="topLabel" class="label text-heading" :class="{ 'label--focused': isFocused }">
      {{ topLabel }}
    </span>

    <span class="input-wrapper">
      <input
        ref="inputRef"
        class="input text-medium"
        :value="modelValue"
        :type="type"
        v-bind="$attrs"
        :style="{ opacity: isFocused ? 1 : 0.3 }"
        @input="onInput"
        @focus="onFocus"
        @blur="onBlur"
      />

      <slot name="append" />
    </span>
  </label>
</template>

<script setup lang="ts">
import { ref } from 'vue';

interface BaseInputProps {
  modelValue: string;
  topLabel?: string;
  type?: HTMLInputElement['type'];
}

withDefaults(defineProps<BaseInputProps>(), {
  type: 'text',
});

const emit = defineEmits<{
  (e: 'update:modelValue', value: string): void;
  (e: 'focus', ev: FocusEvent): void;
  (e: 'blur', ev: FocusEvent): void;
}>();

const isFocused = ref(false);
const inputRef = ref<HTMLInputElement | null>(null);

const onInput = (e: Event) => {
  emit('update:modelValue', (e.target as HTMLInputElement).value);
};

const onFocus = (e: FocusEvent) => {
  isFocused.value = true;
  emit('focus', e);
};

const onBlur = (e: FocusEvent) => {
  isFocused.value = false;
  emit('blur', e);
};

defineExpose({
  inputEl: inputRef,
});
</script>

<style scoped>
.wrapper {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.input-wrapper {
  display: flex;
  align-items: center;
  gap: 12px;
}

.label {
  text-align: start;
  color: var(--color-dark);
  transition: color 0.3s ease-in-out;
}

.label--focused {
  color: var(--color-primary);
}

.input {
  padding: 6px 8px;
  border-radius: 8px;
  border: 1px solid var(--color-accent);
  outline: none;
  color: var(--color-dark);
  transition:
    opacity 0.3s ease,
    width 0.3s ease;
}
</style>
