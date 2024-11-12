<template>
  <div class="wrapper">
    <h1>{{ titlePage }}</h1>
    <div class="input-wrapper">
      <input
        type="text"
        name="inputTodo"
        :placeholder="inputPlaceholder"
        id="inputTodo"
        v-model.trim="inputText"
        @keyup.enter="addInputText"
      />
      <button type="button" @click="addInputText" title="Добавить задачу">
        <BtnInputAdd />
      </button>
    </div>

    <h2>{{ activeTasks.length ? titleActiveToDo : notTitleActiveToDo }}</h2>
    <ul class="todo-list" v-if="activeTasks.length">
      <li class="todo-item" v-for="item in activeTasks" :key="item.id" :id="'id-item-' + item.id">
        {{ item.text }}
        <div class="btn-group">
          <button
            type="button"
            class="btn-done"
            @click="doneTodoItem(item.id)"
            title="Завершить задачу"
          >
            <BtnDone />
          </button>
          <button
            type="button"
            class="btn-remove"
            @click="removeItemToDo(item.id)"
            title="Удалить задачу"
          >
            <BtnRemove />
          </button>
        </div>
      </li>
    </ul>

    <h2 v-if="completedTasks.length">{{ titleDoneToDo }}</h2>
    <ul class="todo-list" v-if="completedTasks.length">
      <li
        class="todo-item"
        :class="completedTasks.length ? 'done' : ''"
        v-for="item in completedTasks"
        :key="item.id"
        :id="'id-item-' + item.id"
      >
        {{ item.text }}
        <div class="btn-group">
          <button
            type="button"
            class="btn-done"
            @click="doneTodoItem(item.id)"
            title="Вернуть задачу"
          >
            <BtnUndo />
          </button>
          <button
            type="button"
            class="btn-remove"
            @click="removeItemToDo(item.id)"
            title="Удалить задачу"
          >
            <BtnRemove />
          </button>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
import BtnDone from './components/BtnDone.vue'
import BtnInputAdd from './components/BtnInputAdd.vue'
import BtnRemove from './components/BtnRemove.vue'
import BtnUndo from './components/BtnUndo.vue'
export default {
  components: { BtnDone, BtnRemove, BtnInputAdd, BtnUndo },
  name: 'App',
  created() {
    const savedTasks = localStorage.getItem('tasks')
    if (savedTasks) {
      this.dataItemsToDo = JSON.parse(savedTasks)
    }
  },
  data() {
    return {
      titlePage: 'Список задач',
      titleActiveToDo: 'Активные задачи',
      notTitleActiveToDo: 'Активных задач нет',
      titleDoneToDo: 'Выполненые задачи',
      inputText: '',
      inputPlaceholder: 'Описание новой задачи...',
      dataItemsToDo: [],
    }
  },
  methods: {
    saveToLocalStorage() {
      localStorage.setItem('tasks', JSON.stringify(this.dataItemsToDo))
    },
    addInputText() {
      if (this.inputText != '') {
        this.dataItemsToDo.push({
          text: this.inputText,
          id: this.dataItemsToDo.length + 1,
          completed: false,
        })
        this.inputText = ''
        this.saveToLocalStorage()
      }
    },
    removeItemToDo(id) {
      this.dataItemsToDo = this.dataItemsToDo.filter((item) => item.id !== id)
      this.saveToLocalStorage()
    },
    doneTodoItem(id) {
      const item = this.dataItemsToDo.find((item) => item.id === id)
      if (item) {
        item.completed = !item.completed
        this.saveToLocalStorage()
      }
    },
  },
  computed: {
    activeTasks() {
      return this.dataItemsToDo.filter((item) => !item.completed)
    },
    completedTasks() {
      return this.dataItemsToDo.filter((item) => item.completed)
    },
  },
}
</script>

<style src="./assets/styles.css"></style>
