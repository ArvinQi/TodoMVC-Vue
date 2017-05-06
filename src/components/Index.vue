<template>
  <div id="todoapp">
    <section class="todoapp">
      <header class="header">
        <h1>todos</h1>
        <input autofocus="autofocus" @keyup.enter="addTodo" v-model="newTodo" autocomplete="off" placeholder="What needs to be done?"
          class="new-todo">
      </header>
      <section class="main" v-if="todos.length>0 || type != 'all'">
        <input type="checkbox" class="toggle-all" v-model="completedAll">
        <label for="toggle-all" @click="toggleAll">Mark all as complete</label>
        <ul class="todo-list">
          <li v-for="todo in todos" :class="{completed: todo.completed,'editing': todo.editing}">
            <div class="view" v-if="!todo.editing">
              <input class="toggle" type="checkbox" v-model="todo.completed">
              <label for="toggle" v-on:dblclick="editTodo(todo)">{{todo.title}}</label>
              <button class="destroy" @click="removeTodo(todo)"></button>
            </div>
            <input class="edit" type="text" v-model="editingTodo" v-auto-focus @blur="finishEdit(todo)" @keyup.enter="finishEdit(todo)"
              @keyup.esc="cancelEdit(todo)" v-else>
          </li>
        </ul>
      </section>
      <footer class="footer" v-if="todos.length>0 || type != 'all'">
        <span class="todo-count">
        <strong>{{left}}</strong> items left
				</span>
        <ul class="filters">
          <li><a href="#/all" :class="{selected:type=='all'}">All</a></li>
          <li><a href="#/active" :class="{selected:type=='active'}">Active</a></li>
          <li><a href="#/completed" :class="{selected:type=='completed'}">Completed</a></li>
        </ul>
        <button class="clear-completed" @click="clearCompleted" v-if="todoList.length>left">
					Clear completed
				</button>
      </footer>
    </section>
    <footer id="info">
      <p>Double-click to edit a todo</p>
      <p>Created by <a href="http://ArvinQi.github.io">Arvin Qi</a></p>
      <p>Part of <a href="http://todomvc.com">TodoMVC</a></p>
    </footer>
  </div>
</template>
<script>
  export default {
    name: 'todoapp',
    data() {
      return {
        todoList: [{
            title: 'first',
            completed: false
          },
          {
            title: 'first',
            completed: false
          },
          {
            title: 'first',
            completed: true
          }
        ],
        newTodo: '',
        oldEditTodo: '',
        editingTodo: '',
        // completedAll: false
      }
    },
    computed: {
      todos: function () {
        return this.todoList.filter(item => {
          if (this.type === 'active') {
            return !item.completed
          } else if (this.type === 'completed') {
            return item.completed
          } else {
            return true
          }
        })
      },
      type: function () {
        return this.$route.params.type || 'all'
      },
      left: function () {
        return this.todoList.filter(item => {
          return !item.completed
        }).length
      },
      completedAll: function () {
        return this.todoList.filter(item => {
          return item.completed
        }).length == this.todoList.length
      }
    },
    directives: {
      // 'auto-focus': function (value) {
      // if (!value) {
      //     return;
      // }
      // var el = this.el;
      // this.$nextTick(function () {
      //     el.focus();
      // });
      // }
      'auto-focus': {
        inserted: function (el) {
          // 聚焦元素
          el.focus(true)
        }
      },
      // 'auto-focus': function (el, binding) {
      //   console.log(el, binding.value)
      //   if (binding.value) {
      //     el.focus();
      //   }
      // }
    },
    watch: {
      // completedAll: function() {
      //   this.todoList = this.todoList.map(item=>{
      //     item.completed = this.completedAll
      //     return item
      //   })
      // },
      // left: function() {
      //   this.completedAll = this.todoList.filter(item => {
      //       return item.completed
      //     }).length == this.todoList.length
      // }
    },
    methods: {
      addTodo() {
        if (this.newTodo) {
          let value = this.newTodo.trim();
          this.todoList.push({
            title: value,
            completed: false
          })
          this.newTodo = ''
        }
      },
      removeTodo(todo) {
        let index = this.todoList.indexOf(todo);
        this.todoList.splice(index, 1);
      },
      editTodo(todo) {
        let index = this.todoList.indexOf(todo);
        this.oldEditTodo = todo.title;
        this.editingTodo = todo.title;
        todo.editing = true;
        this.todoList.splice(index, 1, todo);
      },
      finishEdit(todo) {
        if (!this.oldEditTodo) return
        let index = this.todoList.indexOf(todo);
        todo.title = this.editingTodo;
        todo.editing = false;
        this.todoList.splice(index, 1, todo);

      },
      cancelEdit(todo) {
        this.oldEditTodo = '';
        let index = this.todoList.indexOf(todo);
        todo.editing = false;
        this.todoList.splice(index, 1, todo);

      },
      toggleAll() {
        let willCompleted;
        if (this.completedAll) {
          willCompleted = false;
        } else {
          willCompleted = true;
        }
        this.todoList = this.todoList.map(item => {
          item.completed = willCompleted
          return item
        })
      },
      clearCompleted() {
        this.todoList = this.todoList.filter(item => {
          return !item.completed
        })
      }
    }
  }

</script>
<style scoped>
  @import '~todomvc-app-css/index.css';
  .toggle {
    left: 0;
  }
  /*.toggle-all{
    width: 60px;
    height: 34px;
    font-size: 0;
    position: absolute;
    top: -52px;
    left: -13px;
    z-index: 1000;
  }*/

</style>
