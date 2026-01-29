<template>
  <BaseInput
    ref="baseInputRef"
    :model-value="formatted"
    :top-label="topLabel"
    inputmode="numeric"
    pattern="[0-9 ]*"
    :style="inputStyle"
    @update:model-value="onInput"
    @focus="emit('focus', $event)"
    @blur="emit('blur', $event)"
  >
    <template v-if="appendLabel" #append>
      <span class="append-label">{{ appendLabel }}</span>
    </template>
  </BaseInput>
</template>

<script setup lang="ts">
import { type ComponentPublicInstance, computed, onMounted, ref } from 'vue';
import { BaseInput, type BaseInputExposed } from '../../../shared/ui';

const MAX = Number.MAX_SAFE_INTEGER;

interface HoursInputProps {
  modelValue: number;
  topLabel?: string;
  appendLabel?: string;
  minWidthPx?: number;
}

const props = withDefaults(defineProps<HoursInputProps>(), {
  modelValue: 0,
  topLabel: 'SAMUEL IS',
  appendLabel: 'hours old',
  minWidthPx: 72,
});

const emit = defineEmits<{
  (e: 'update:modelValue', value: number): void;
  (e: 'focus', ev: FocusEvent): void;
  (e: 'blur', ev: FocusEvent): void;
}>();

const baseInputRef = ref<ComponentPublicInstance<BaseInputExposed> | null>(null);

const getInputEl = (): HTMLInputElement | null => {
  const el = baseInputRef.value?.inputEl ?? null;
  return el instanceof HTMLInputElement ? el : null;
};

const onlyDigits = (v: string) => v.replace(/\D/g, '');

const formatGrouped = (v: string) => v.replace(/\B(?=(\d{3})+(?!\d))/g, ' ');

const raw = computed(() => (props.modelValue > 0 ? String(props.modelValue) : ''));

const formatted = computed(() => formatGrouped(raw.value));

const onInput = (input: string) => {
  const digits = onlyDigits(input);

  if (!digits) {
    emit('update:modelValue', 0);
    return;
  }

  const maxLength = String(MAX).length;
  const trimmed = digits.slice(0, maxLength);

  const next = Number(trimmed);

  if (next > MAX) {
    return;
  }

  emit('update:modelValue', next);
};

const inputStyle = computed(() => {
  const length = Math.max(formatted.value.length, 1);
  return {
    width: `max(${props.minWidthPx}px, ${length + 1}ch)`,
  };
});

const onBeforeInput = (e: InputEvent) => {
  if (!e.data) return;

  const next = onlyDigits(formatted.value + e.data);
  const maxLength = String(MAX).length;

  if (next.length > maxLength || Number(next) > MAX) {
    e.preventDefault();
  }
};

onMounted(() => {
  const input = getInputEl();

  if (!input) return;

  input.addEventListener('beforeinput', onBeforeInput);
});
</script>
<style scoped>
.append-label {
  white-space: nowrap;
}
</style>
