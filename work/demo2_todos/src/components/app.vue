<template>
  <div class="todo-container">
    <div class="todo-wrap">
      <demo-header @add_todo="add"></demo-header>
      <list :todos="todos" :delete-todo="deleteTodo"></list>
      <demo-footer :todos="todos" :remove-completed="removeCompleted" ref="footer"></demo-footer>
    </div>
  </div>
</template>
<script>
  import header from './header.vue'
  import list from './list.vue'
  import footer from './footer.vue'
  import localStorageUtil from '../util/localStorageUtil'

  export default {

    data () {
      return {
        todos: []
      }
    },

    created () {
      // 从local中读取
      this.todos = localStorageUtil.getTodos()

      // 绑定自定义监听(给footer组件对象)
      // this.$refs.footer.$on('selectAllTodos', this.selectAllTodos)  // 不能使用, 太早了, 模板还没有编译
    },

    mounted () {
      // 绑定自定义监听(给footer组件对象)
      this.$refs.footer.$on('selectAllTodos', this.selectAllTodos)
    },

    methods: {
      add (todo) {
        this.todos.unshift(todo)
      },
      deleteTodo (index) {
        this.todos.splice(index, 1)
      },
      removeCompleted () {
        this.todos = this.todos.filter(todo => !todo.complete)
      },
      selectAllTodos (isSelect) {
        this.todos.forEach(todo => {
          todo.complete = isSelect
        })
      }
    },

    watch: {
      todos: {
        deep: true, // 深度监视
        /*handler: function (val) { // todos发生了变化
         localStorageUtil.saveTodos(val) // 保存todos
         },*/
        handler: localStorageUtil.saveTodos
      }
    },

    components: {
      'demo-header': header,
      list,
      'demo-footer': footer
    }
  }
</script>
<style>
  .todo-container {
    width: 600px;
    margin: 0 auto;
  }
  .todo-container .todo-wrap {
    padding: 10px;
    border: 1px solid #ddd;
    border-radius: 5px;
  }
</style>