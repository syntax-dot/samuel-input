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
        :inputmode="numeric ? 'numeric' : undefined"
        :pattern="numeric ? '[0-9]*' : undefined"
        v-bind="$attrs"
        :style="{ opacity: isFocused ? 1 : 0.3 }"
        @beforeinput="onBeforeInput"
        @input="onInput"
        @keydown="onKeyDown"
        @paste="onPaste"
        @focus="onFocus"
        @blur="onBlur"
      />

      <slot name="append"></slot>
    </span>
  </label>
</template>

<script setup lang="ts">
import { ref } from 'vue';

interface BaseInputProps {
  modelValue: string;
  topLabel?: string;
  type?: string;
  numeric?: boolean;
}

const props = withDefaults(defineProps<BaseInputProps>(), {
  type: 'text',
  numeric: false,
});

const emit = defineEmits<{
  (e: 'update:modelValue', value: string): void;
  (e: 'focus', ev: FocusEvent): void;
  (e: 'blur', ev: FocusEvent): void;
}>();

const isFocused = ref(false);
const inputRef = ref<HTMLInputElement | null>(null);

const onFocus = (ev: FocusEvent) => {
  isFocused.value = true;
  emit('focus', ev);
};

const onBlur = (ev: FocusEvent) => {
  isFocused.value = false;
  emit('blur', ev);
};

const onInput = (e: Event) => {
  emit('update:modelValue', (e.target as HTMLInputElement).value);
};

const onBeforeInput = (e: InputEvent) => {
  if (!props.numeric) return;
  if (e.data && /\D/.test(e.data)) {
    e.preventDefault();
  }
};

const onKeyDown = (e: KeyboardEvent) => {
  if (!props.numeric) return;

  const allowed = ['Backspace', 'Delete', 'ArrowLeft', 'ArrowRight', 'Tab'];

  if (allowed.includes(e.key)) return;
  if (/^\d$/.test(e.key)) return;

  e.preventDefault();
};

const onPaste = (e: ClipboardEvent) => {
  if (!props.numeric) return;

  const text = e.clipboardData?.getData('text') ?? '';
  if (!/^\d+$/.test(text)) e.preventDefault();
};
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
