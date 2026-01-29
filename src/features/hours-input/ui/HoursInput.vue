<template>
  <BaseInput
    ref="baseInputRef"
    :model-value="formatted"
    top-label="SAMUEL IS"
    numeric
    @update:model-value="handleInput"
    @focus="emit('focus', $event)"
    @blur="emit('blur', $event)"
  />
</template>

<script setup lang="ts">
import { computed, nextTick, ref, watch } from 'vue';
import { BaseInput } from '../../../shared/ui';

interface HoursInputProps {
  modelValue: number;
}

const props = withDefaults(defineProps<HoursInputProps>(), {
  modelValue: 0,
});

const emit = defineEmits<{
  (e: 'update:modelValue', value: number): void;
  (e: 'focus', ev: FocusEvent): void;
  (e: 'blur', ev: FocusEvent): void;
}>();

const baseInputRef = ref<InstanceType<typeof BaseInput> | null>(null);

const format = (value: number) => value.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ' ');

const formatted = computed(() => format(props.modelValue));

const handleInput = (value: string) => {
  const digits = value.replace(/\D/g, '');
  const numeric = digits ? Number(digits) : 0;
  emit('update:modelValue', numeric);
};

const updateWidth = async () => {
  await nextTick();

  const inputEl = (baseInputRef.value?.$el as HTMLElement)?.querySelector('input');

  if (!inputEl) return;

  const span = document.createElement('span');
  span.style.visibility = 'hidden';
  span.style.position = 'absolute';
  span.style.whiteSpace = 'pre';
  span.style.font = getComputedStyle(inputEl).font;
  span.textContent = formatted.value || '0';

  document.body.appendChild(span);
  const width = Math.max(72, span.offsetWidth + 16);
  inputEl.style.width = `${width}px`;
  document.body.removeChild(span);
};

watch(formatted, updateWidth, { immediate: true });
</script>
