<template>
  <table class="w-full">
    <thead>
      <tr>
        <th>Описание задачи</th>
        <th>Дата</th>
        <th>Начало выполнения</th>
        <th>Конец выполнения</th>
        <th>Итоговое время</th>
      </tr>
    </thead>
    <tbody class="text-center">
      <tr v-for="time in times" :key="time.id">
        <td>{{ time.desc }}</td>
        <td>{{ time.date }}</td>
        <td>{{ time.start }}</td>
        <td>{{ time.stop }}</td>
        <td>{{ time.total }}</td>
        <td><app-button @action="deleteItem(time.id)">Удалить</app-button></td>
      </tr>
    </tbody>
  </table>
</template>

<script>
import AppButton from './AppButton.vue'

export default {
  emits: ['delete-item'],
  props: ['times'],
  methods: {
    deleteItem(id) {
      this.$emit('delete-item', id)
    }
  },
  components: {
    AppButton,
  }
}
</script>
