<template>
  <BaseInput
    ref="baseInputRef"
    :model-value="formatted"
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
import { computed, nextTick, ref, watch } from 'vue';
import { BaseInput } from '../../../shared/ui';

const DEFAULT_TOP_LABEL = 'SAMUEL IS';
const DEFAULT_APPEND_LABEL = 'hours old';

interface HoursInputProps {
  modelValue: number;
  topLabel?: string;
  appendLabel?: string;
}

const props = withDefaults(defineProps<HoursInputProps>(), {
  modelValue: 0,
  topLabel: DEFAULT_TOP_LABEL,
  appendLabel: DEFAULT_APPEND_LABEL,
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
