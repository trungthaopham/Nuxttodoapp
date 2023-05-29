<template>
  <div class="container">
    <h1>Todos</h1>
    <div class="main-content">
      <input
        class="todo-input"
        placeholder="What needs to be done?"
        @keyup.enter="addTodo"
      />
      <span>(Confirm by enter)</span>
      <h3 class="title">List Todos</h3>
      <ul class="todo-list">
        <li
          v-for="(todo, index) in filterTodos"
          :key="index"
          style="padding: 4px;"
          @click.self="toggle(todo)"
        >
          <span
            :class="{ done: todo.isComplete, notDone: !todo.isComplete, hide: isEdit === todo.id }"
            @click.self="toggle(todo)"
            >{{ todo.content }}</span
          >
          <input
            v-if="isEdit === todo.id"
            v-model="content"
            class="input-edit"
            type="text"
            @keydown.enter.stop="editTodo(todo)"
          />
          <div>
            <button class="edit-text" @click.stop="clickEdit(todo)">Edit</button>
            <button class="complete-text" @click.stop="toggle(todo)">Complete</button>
            <button class="delete-text" @click.stop="deleteTodo(todo)"
              >Delete</button
            >
          </div>
        </li>
      </ul>
      <div class="filter">
        <button class="all-border" @click="clickAll">All({{ total }})</button>
        <button class="progress-border edit-text" @click="clickProgress"
          >Progess({{ countProgress }})</button
        >
        <button class="done-border complete-text" @click="clickDone"
          >Done({{ countDone }})</button
        >
      </div>
    </div>
  </div>
</template>


<script>
export default {
  name: 'TodoComponent',
  data() {
    return {
      todoList: [],
      filter: 'all',
      isEdit: -1,
      content: '',
    }
  },
    fetch({store}) {
    return store.dispatch("todos/getTodoList", );
  },
  computed: {
    filterTodos() {
      return this.$store.getters[`todos/${this.filter}`]
    },
    total() {
      return this.$store.state.todos.todoList.length
    },
    countProgress() {
      return this.$store.state.todos.todoList.filter(function (item) {
        return !item.isComplete
      }).length
    },
    countDone() {
      return this.total - this.countProgress
    },
  },
  methods: {
    async addTodo(e) {
    if (e.target.value.length) {
      await this.$store.dispatch('todos/addTodo', e.target.value).then( res => {
        this.$toaster.success(`Created success!`, {timeout: 3000});
        e.target.value = '';
        }).catch(err => {
        console.log(err)
        this.$toaster.error('Create failed!', {timeout: 3000});
      })
    }
    },
    clickEdit(todo) {
      this.isEdit = todo.id
      this.content = todo.content
      },
    editTodo(todo) {
      if (this.content.length) {
        this.$store.dispatch('todos/editTodo', { todo, content: this.content });
        this.isEdit = -1;
        this.$toaster.success(`Updated todo success!`, {timeout: 3000});
        this.content = '';
      }
    },
    toggle(todo) {
    this.$store.dispatch('todos/toggleTodo', todo)
    },
    deleteTodo(todo) {
    this.$store.dispatch('todos/deleteTodo', todo);
    this.$toaster.success(`Deleted todo success!`, {timeout: 3000});
    },
    clickAll() {
      this.filter = 'all'
    },
    clickProgress() {
      this.filter = 'progress'
    },
    clickDone() {
      this.filter = 'done'
    },
  }
}
</script>

<style scoped>
.container {
  width: 800px;
  margin: 0 auto;
}
.container h1{
  font-size: 50px;
  color: cadetblue;
  text-align: center;
}
.container .todo-input{
  width: 100%;
  font-size: 24px;
  padding: 5px;
}

.todo-list li{
  display: flex;
  justify-content: space-between;
}
.edit-text,.delete-text,.complete-text{
  border-width: 1px;
  border-color: #333333;
  border-radius: 4px;
  padding: 4px 8px;
  width: 100px;
}
.edit-text{
  background-color: rgb(93, 158, 215);
}
.complete-text{
  background-color: rgb(74, 238, 110);
}
.delete-text{
  background-color: rgb(245, 62, 83);
}
.notDone{
  color:rgb(2, 6, 42)
}
.done{
  color:rgb(110, 228, 51);
  text-decoration-line: line-through;
}
span{
  font-size: 20px;
}
button{
  font-size: 20px;
  padding: 4px 20px
}
.input-edit{
  border-radius: 2px solid #313131;
  padding: 2px;
  font-size: 16px;
}

.title{
  text-align: center
}
.filter > button{
  padding: 2px 8px;
  min-width: 120px;
}
</style>
