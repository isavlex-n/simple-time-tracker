<template>
  <div>
    <label :for="id">{{ label }}</label>
    <input
      class="font-sans block text-sm w-full py-2 px-3 ring-1 ring-slate-900/10 text-slate-500 rounded-lg shadow-sm dark:bg-slate-800 dark:ring-0 dark:highlight-white/5 dark:text-slate-400"
      type="text"
      :value="modelValue"
      :id="id"
      :placeholder="placeholder"
      @input="change"
      :disabled="disabled"
    />
    <small v-if="error">{{ error }}</small>
  </div>
</template>

<script>
export default {
  emits: ['update:modelValue'],
  props: {
    modelValue: String,
    placeholder: String,
    label: String,
    error: String,
    disabled: Boolean,
  },
  data() {
    return {
      id: 'input-' + Math.random(),
    }
  },
  methods: {
    change(event) {
      this.$emit('update:modelValue', event.target.value)
    },
  },
}
</script>
