<template>
  <BaseInput
    ref="baseInputRef"
    :model-value="displayValue"
    :top-label="topLabel"
    numeric
    @update:model-value="handleInput"
    @focus="emit('focus', $event)"
    @blur="emit('blur', $event)"
  >
    <template v-if="appendLabel" #append>
      <span>{{ appendLabel }}</span>
    </template>
  </BaseInput>
</template>

<script setup lang="ts">
import { type ComponentPublicInstance, computed, nextTick, onBeforeUnmount, ref, watch } from 'vue';
import { BaseInput, type BaseInputExposed } from '../../../shared/ui';

const DEFAULT_TOP_LABEL = 'SAMUEL IS';
const DEFAULT_APPEND_LABEL = 'hours old';

interface HoursInputProps {
  modelValue: number;
  topLabel?: string;
  appendLabel?: string;
  minWidthPx?: number;
}

const props = withDefaults(defineProps<HoursInputProps>(), {
  modelValue: 0,
  topLabel: DEFAULT_TOP_LABEL,
  appendLabel: DEFAULT_APPEND_LABEL,
  minWidthPx: 72,
});

const emit = defineEmits<{
  (e: 'update:modelValue', value: number): void;
  (e: 'focus', ev: FocusEvent): void;
  (e: 'blur', ev: FocusEvent): void;
}>();

const baseInputRef = ref<ComponentPublicInstance<BaseInputExposed> | null>(null);

const formatGrouped = (value: number) => {
  const raw = Math.max(0, Number.isFinite(value) ? Math.trunc(value) : 0).toString();
  return raw.replace(/\B(?=(\d{3})+(?!\d))/g, ' ');
};

const displayValue = computed(() => formatGrouped(props.modelValue));

const handleInput = (value: string) => {
  const digits = value.replace(/\D/g, '');
  emit('update:modelValue', digits ? Number(digits) : 0);
};

const getInputEl = (): HTMLInputElement | null => {
  const el = baseInputRef.value?.inputEl ?? null;
  return el instanceof HTMLInputElement ? el : null;
};

const canvas = document.createElement('canvas');
const ctx = canvas.getContext('2d');

const measureTextWidth = (text: string, font: string) => {
  if (!ctx) return text.length * 10;
  ctx.font = font;
  return ctx.measureText(text).width;
};

const updateWidth = async () => {
  await nextTick();

  const inputEl = getInputEl();
  if (!inputEl) return;

  const styles = getComputedStyle(inputEl);
  const font = styles.font;
  const paddingLeft = Number.parseFloat(styles.paddingLeft || '0') || 0;
  const paddingRight = Number.parseFloat(styles.paddingRight || '0') || 0;

  const text = displayValue.value || '0';
  const textWidth = measureTextWidth(text, font);
  const width = Math.ceil(textWidth + paddingLeft + paddingRight);

  inputEl.style.width = `${Math.max(props.minWidthPx, width)}px`;
};

watch(displayValue, updateWidth, { immediate: true });

let ro: ResizeObserver | null = null;

watch(
  () => baseInputRef.value,
  async () => {
    await nextTick();

    const inputEl = getInputEl();
    if (!inputEl) return;

    ro?.disconnect();
    ro = new ResizeObserver(() => {
      void updateWidth();
    });

    ro.observe(inputEl);
    void updateWidth();
  },
  { immediate: true },
);

onBeforeUnmount(() => {
  ro?.disconnect();
  ro = null;
});
</script>
