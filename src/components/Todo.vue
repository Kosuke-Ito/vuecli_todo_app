<template>
  <div>
    <h1>{{ todo_app_title }}</h1>
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

    <button class="toggle-form" v-on:click='toggle = !toggle'>
      <span v-if="!toggle">
        +
      </span>
      <span v-else>
        x
      </span>
    </button>
    <form class="add-form" v-on:submit.prevent="doAdd" v-show='toggle'>
      <div class="form-list">タイトル<input type="text" ref="title"></div>
      <div class="form-list">内容<input type="text" ref="content"></div>
      <button type="submit">追加</button>
    </form>
  </div>


</template>

<script>
  import { todoStorage } from './TodoStorage.js'

  export default {
    // isShow: trueの時 コンテンツと編集と削除が表示される
    name: 'Todo',
    props: { todo_app_title: String },
    data: function () {
      return {
        todos: [],
        toggle: false,
        showingId: null,
        textInput: ""
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
        const title = this.$refs.title
        const content = this.$refs.content

        if (!title.value.length || !content.value.length) {
          return
        }
        this.todos.push({
          id: todoStorage.uid++,
          title: title.value,
          content: content.value,
        })
        title.value = ""
        content.value = ""
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
        const [title, content] = titleContent.split('\n')
        this.$set(this.todos[todo.id], 'title', title)
        this.$set(this.todos[todo.id], 'content', content)
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
  font-size: 1rem;
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
input {
  margin-left: .5rem
}
button {
  margin-left:.5rem;
}
</style>
