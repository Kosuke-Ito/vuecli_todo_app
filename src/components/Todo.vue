<template>
  <div>
    <h1>{{ todo_app_title }}</h1>
    <h2>タスク一覧</h2>
    <ul>
      <li 
        v-for="todo in todos"
        v-bind:key="todo.id"
        v-on:click="showTodo(todo)">
          {{ todo.title }}
          <span v-bind:style="changeDisplay(todo)">
            {{todo.content}}
          </span>
      </li>
    </ul>

    <h3>新しいタスクの追加</h3>
    <form class="add-form" v-on:submit.prevent="doAdd">
      <div class="form-list">タイトル<input type="text" ref="title"></div>
      <div class="form-list">内容<input type="text" ref="content"></div>
      <button type="submit">追加</button>
    </form>
  </div>


</template>

<script>
  let STORAGE_KEY = 'todos-vuejs-demo'
  let todoStorage = {
    fetch: function() {
      let todos = JSON.parse(
        localStorage.getItem(STORAGE_KEY) || '[]'
      )
      todos.forEach(function(todo, index) {
        todo.id = index
      })
      todoStorage.uid = todos.length
      return todos
    },
    save: function(todos) {
      localStorage.setItem(STORAGE_KEY, JSON.stringify(todos))
    }
  }


  export default {
    // isShow: trueの時 コンテンツと編集と削除が表示される
    name: 'Todo',
    props: { todo_app_title :String },
    data: function () {
      return {
        todos: [],
        isShow: false
      }
    },
    created() {
      this.todos = todoStorage.fetch()
    },
    watch: {
      todos: {
        handler: function(todos) {
          todoStorage.save(todos)
        },
        deep: true
      }
    },
    methods: {
      doAdd: function () {
        let title = this.$refs.title
        let content = this.$refs.content

        if (!title.value.length || !content.value.length) {
          return
        }
        this.todos.push({
          id: todoStorage.uid++,
          title: title.value,
          content: content.value,
          isShowDetail: false 
        })
        title.value = ""
        content.value = ""
      },
    }
  }
</script>

<style scoped>
ul{
  padding: 0 0 0 .5rem;
}
li {
  list-style-type: none;
  margin-bottom: .5rem;
}
li > button{
  font-size: .3rem;
  padding: .05rem .25rem;
}
.form-list{
  margin-bottom:.3rem;
}
input {
  margin-left: .5rem
}
button {
  margin-left:.5rem;
}
</style>
