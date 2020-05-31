<template>
  <div id="app">
    <h1>TODO</h1>
    <input
      type="text"
      placeholder="todoを入力してください"
      v-model="newTodo"
      @keyup.enter="addTodo"
    />
    <draggable v-model="todos" @end="saveTodos()">
      <div v-for="(todo, index) in todos" :key="index">
        <input type="checkbox" v-model="todo.completed" />
        <span
          v-if="!todo.editing"
          @dblclick="editTodo(todo)"
          :class="{ done: todo.completed }"
        >
          タイトル：{{ todo.title }}
        </span>
        <input
          type="text"
          v-if="todo.editing"
          v-model="todo.title"
          @blur="doneEdit(todo)"
          @keyup.enter="doneEdit(todo)"
          v-focus
        />
        作成日：{{ todo.created | createdmoment }}
        <span> 期日：{{ todo.deadline | moment }}</span>
        <span>完了日:{{ todo.compdate | moment }}</span>

        <button @click="removeTodo(index)">削除</button>
        <button @click="completeTodo(todo)">完了</button>
      </div>
    </draggable>

    <draggable v-model="completetodos"> </draggable>

    <div><input type="checkbox" />Check all</div>
    <div>残り{{ remaining }}のアイテム</div>
    <button @click="sortNameAsc()">名前順(昇順)</button>
    <button @click="sortNameDesc()">名前順（降順）</button>
    <button @click="sortCreatedAsc()">作成日順(昇順)</button>
    <button @click="sortCreatedDesc()">作成日順（降順）</button>
    <button @click="sortDeadAsc()">期日順（昇順）</button>
    <button @click="sortDeadDesc()">期日順（降順）</button>
    <button @click="sortCompAsc()">完了日順（昇順）</button>
    <button @click="sortCompDesc()">完了日順（降順）</button>
  </div>
</template>

<script>
import moment from 'moment';
import draggable from 'vuedraggable';

export default {
  components: {
    draggable,
  },
  filters: {
    createdmoment: function(date) {
      console.log(date);
      return moment(date).format('YYYY/MM/DD HH:mm:ss');
    },
    moment: function(date) {
      console.log(date);
      return moment(date).format('YYYY/MM/DD');
    },
  },
  data() {
    return {
      todos: [],
      completetodos: [{ dummy: 'dummydata' }],
      pendingtodos: [],
      newTodo: '',
    };
  },

  directives: {
    focus: {
      // ディレクティブ定義
      inserted: function(el) {
        el.focus();
      },
    },
  },
  mounted() {
    // todos[]から、要素を取得できたら
    if (localStorage.getItem('todos')) {
      try {
        // todosを、JSON形式に変換する
        this.todos = JSON.parse(localStorage.getItem('todos'));
      } catch (e) {
        localStorage.removeItem('todos');
      }
    }
  },
  computed: {
    remaining() {
      return this.todos.filter((todo) => !todo.completed).length;
    },
  },
  methods: {
    addTodo() {
      if (!this.newTodo) {
        return;
      }
      let dead = window.prompt(
        '期日を入力してください (YYYYMMDD,YYYY-MM-DD形式にすること)'
      );

      const params = {
        title: this.newTodo,
        deadline: dead,
        compdate: '',
        editing: false,
        completed: false,
        created: new Date(),
      };
      this.todos.push(params);
      this.newTodo = '';
      this.saveTodos();
    },
    removeTodo(index) {
      this.todos.splice(index, 1);
      this.saveTodos();
    },
    editTodo(todo) {
      todo.editing = true;
    },
    doneEdit(todo) {
      todo.editing = false;
      this.saveTodos();
    },
    completeTodo(todo) {
      todo.compdate = new Date();
      todo.completed = true;
    },
    sortNameAsc() {
      let name = this.todos;
      name.sort(function(a, b) {
        if (a.title > b.title) {
          return 1;
        } else {
          return -1;
        }
      });
      console.log(name);
    },
    sortNameDesc() {
      let name = this.todos;
      name.sort(function(a, b) {
        if (a.title < b.title) {
          return 1;
        } else {
          return -1;
        }
      });
      console.log(name);
    },
    sortCreatedAsc() {
      let cre = this.todos;
      cre.sort(function(a, b) {
        if (a.created > b.created) {
          return 1;
        } else {
          return -1;
        }
      });
      console.log(cre);
    },
    sortCreatedDesc() {
      let cre = this.todos;
      cre.sort(function(a, b) {
        if (a.created < b.created) {
          return 1;
        } else {
          return -1;
        }
      });
      console.log(cre);
    },
    sortDeadAsc() {
      let d = this.todos;
      d.sort(function(a, b) {
        if (a.deadline > b.deadline) {
          return 1;
        } else {
          return -1;
        }
      });
    },
    sortDeadDesc() {
      let d = this.todos;
      d.sort(function(a, b) {
        if (a.deadline < b.deadline) {
          return 1;
        } else {
          return -1;
        }
      });
    },
    sortCompAsc() {
      let c = this.todos;
      c.sort(function(a, b) {
        if (a.compdate > b.compdate) {
          return 1;
        } else {
          return -1;
        }
      });
    },
    sortCompDesc() {
      let c = this.todos;
      c.sort(function(a, b) {
        if (a.compdate < b.compdate) {
          return 1;
        } else {
          return -1;
        }
      });
    },
    saveTodos() {
      const parsed = JSON.stringify(this.todos);
      localStorage.setItem('todos', parsed);
    },
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

#nav {
  padding: 30px;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
}

#nav a.router-link-exact-active {
  color: #42b983;
}
.done {
  text-decoration: line-through;
}
</style>
