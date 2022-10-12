<template>
  <button
    class="py-2 px-3 bg-cyan-500 hover:bg-cyan-600 text-white text-sm font-semibold rounded-md shadow focus:outline-none disabled:opacity-50"
    :class="color"
    @click="$emit('action')"
  >
    <slot />
  </button>
</template>
<script>
export default {
  emits: ['action'],
  props: {
    color: {
      type: String,
      default: '',
    },
  },
}
</script>
