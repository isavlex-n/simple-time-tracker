<template>
  <table class="w-full">
    <thead>
      <tr>
        <th>Описание задачи</th>
        <th>Дата</th>
        <th>Итоговое время</th>
      </tr>
    </thead>
    <tbody class="text-center">
      <tr v-for="time in times" :key="time.id">
        <td
          @click="clickItem(time.id)"
          class="hover:bg-neutral-100 hover:cursor-pointer"
        >
          {{ time.desc }}
          <app-history-list-item
            :history-list="time.history"
            :is-show="time.isShow"
          ></app-history-list-item>
        </td>
        <td>{{ time.date }}</td>
        <td>{{ getFormattedTime(time.total) }}</td>
        <td>
          <app-button @action="startPause(time.id)">{{
            time.isActive ? 'Пауза' : 'Старт'
          }}</app-button>
        </td>
        <td><app-button @action="deleteItem(time.id)">Удалить</app-button></td>
      </tr>
    </tbody>
  </table>
</template>

<script>
import AppButton from './AppButton.vue'
import outputTime from '../utils/outputTime'
import AppHistoryListItem from './AppHistoryListItem.vue'
export default {
  emits: ['delete-item', 'start-pause', 'click-item'],
  props: ['times'],
  methods: {
    deleteItem(id) {
      this.$emit('delete-item', id)
    },
    getFormattedTime(total) {
      return outputTime(Math.floor(total / 1000))
    },
    startPause(id) {
      this.$emit('start-pause', id)
    },
    clickItem(id) {
      this.$emit('click-item', id)
    },
  },
  components: {
    AppButton,
    AppHistoryListItem,
  },
}
</script>
