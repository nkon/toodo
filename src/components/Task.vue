<template>
  <div id="task">
    タスクページ
    <p>current user: {{username}}</p>
  </div>
</template>
<style>
</style>
<script>
import firebase from 'firebase'

export default {
    name: "Task",
    data () {
        return {
            username: firebase.auth().currentUser.displayName,
        };
    },
}
</script>