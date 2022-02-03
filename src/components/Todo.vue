<template>
  <div>
    <h1>{{ todoAppTitle }}</h1>
    <h2>タスク一覧</h2>
    <ul>
      <li 
        v-for="todo in todos"
        v-bind:key="todo.id">
        <h3 v-on:click="showTodo(todo)">{{ todo.title }}</h3>
          
        <div style="display:inline-block;" v-show="showingId === todo.id">
          <form class="content" v-on:submit.prevent="doEdit(textInput, todo)">
            <textarea type="text" style="display:block" v-model.lazy="textInput"/>
            <div style="float: right;">
              <button type="submit">編集</button>
              <button type="button" v-on:click="doRemove(todo)">削除</button>
            </div>
          </form>
        </div>
      </li>
    </ul>

    <button class="toggle-form" v-on:click="openForm()">
      <span v-if="!showingForm">
        +
      </span>
      <span v-else>
        x
      </span>
    </button>
    <form class="add-form" v-on:submit.prevent="doAdd" v-show='showingForm'>
      <div class="form-list">タイトル<input type="text" v-model="newTask.title"></div>
      <div class="form-list">内容<textarea type="text" v-model="newTask.content" /></div>
      <button type="submit">追加</button>
    </form>
  </div>

</template>

<script>
import { todoStorage } from '../TodoStorage.js'

export default {
  name: 'Todo',
  props: { todoAppTitle: String },
  data: function () {
    return {
      todos: [],
      showingForm: false,
      showingId: null,
      textInput: "",
      newTask: {
        title: "",
        content: ""
      }
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
    openForm: function () {
      this.showingForm = !(this.showingForm)
    },
    doAdd: function () {
      const { title, content } = this.newTask
      if (!title.length || !content.length) {
        return
      }
      this.todos.push({
        id: todoStorage.uid++,
        title,
        content,
      })
      this.newTask.title = ""
      this.newTask.content = ""
    },
    showTodo: function (todo) {
      // ひらいているかどうか
      const isCurrentTodoExpanded = this.showingId === todo.id
      if (isCurrentTodoExpanded) {
        // 開いていたら閉じる
        this.showingId = null
      } else {
        // 閉じていたら開く
        this.showingId = todo.id
        this.textInput = todo.title + "\n" + todo.content
      }
    },
    doEdit: function (titleContent, todo) {
      let [title, ...content] = titleContent.split('\n')

      this.$set(this.todos[todo.id], 'title', title)
      this.$set(this.todos[todo.id], 'content', content.join('\n'))
      this.showingId = null
    },
    doRemove: function (todo) {
      const index = this.todos.indexOf(todo)
      this.todos.splice(index, 1)
    }
  }
}
</script>

<style scoped>
ul {
  padding: 0 0 0 .5rem;
}
ul a {
  color:blue;
  text-decoration:none;
}
ul a:hover {
  opacity: 0.3;
}
li {
  list-style-type: none;
  width: fit-content;
}
li h3 {
  margin: 1rem 0 0;
}
li h3:hover {
  opacity: 0.3;
}
li div button {
  margin-left: 0.5rem;
  padding: 0.1rem 0.5rem;
  font-size: 0.5rem;
}
li div .content input {
  white-space: pre-wrap;
  margin: .5rem 0;
  padding: 0.5rem 5rem 0.5rem 1rem;
  border: solid 1px;
  border-radius: 5px;
}
li div p {
  font-size: .8rem;
  margin: .5rem 0;
}
.toggle-form {
  margin: 1rem 0 0;
  font-size: 2rem;
  color:cornflowerblue;
  width: 1.5rem;
  height: 1.5rem;
  background-color: unset;
  border: none;
}
.toggle-form:hover {
  opacity: 0.2;
}
.form-list{
  margin-bottom:.3rem;
}
.add-form  {
  width: fit-content;
}
.add-form textarea {
  margin: .5rem 0 0 .5rem;
}
.add-form button {
  float:right;
}
input {
  margin-left: .5rem
}
button {
  margin-left:.5rem;
}
</style>
