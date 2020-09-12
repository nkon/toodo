<template>
  <div id="task">
    タスクページ
    <p>current user: {{username}}</p>
    <p>current uid: {{uid}}</p>

    <!-- New task -->
    <div>
      <input v-model="newTodoName" placeholder="New Item..."/>
      <button type="submit" v-on:click="createTodo()">ADD</button>
    </div>

    <!-- todo 一覧表示 -->
    <ul>
      <li v-for="(todo, key) in todos" :key="todo.id">
        <span>{{todo.name}}
          <a href="#" v-if="todo.isComplete==true"   v-on:click="updateIsCompleteTodo(todo, key)">✔</a>
          <a href="#" v-if="todo.isComplete==false" v-on:click="updateIsCompleteTodo(todo, key)">❏</a>
        </span>
      </li>
    </ul>
  </div>
</template>

<style>
</style>

<script>
import firebase from "firebase";

export default {
  name: "Task",
  data() {
    return {
      username: firebase.auth().currentUser.displayName,
      database: null,
      todosRef: null,
      todos: [],
      uid: firebase.auth().currentUser.uid,
    };
  },
  created: function () {
    this.database = firebase.database();
    this.uid = firebase.auth().currentUser.uid;
    this.todosRef = this.database.ref("todos/" + this.uid); // '/todos/<uid>/*' 以下を参照する。

    // データに変更があるときに実行される
    var _this = this;
    this.todosRef.on("value", (snapshot) => {
      _this.todos = snapshot.val(); // 再取得して todosに格納する。
    });
  },
  methods: {
    createTodo: function() {
      if (this.newTodoName == ""){
        return;
      }
      this.todosRef.push({
        name: this.newTodoName,
        isComplete: false
      });
      this.newTodoName = "";
    },
    // 完了・未完了の値の更新
    updateIsCompleteTodo: function(todo, key) {
      if (todo.isComplete == true) {
        todo.isComplete = false;
      } else {
        todo.isComplete = true;
      }
      var updates = {};
      updates[key] = todo;
      this.todosRef.update(updates);
    },

  },
};
</script>
