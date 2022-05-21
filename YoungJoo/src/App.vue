<template>
  <div class="container">
    <h2>To-Do List</h2>
     <input
        class="form-control"
        type="text" 
        v-model="searchText"
        placeholder="Search"
      >
      <hr />
    <TodoSimpleForm @add-todo="addTodo" />
 
    <div v-if="!filterdTodos.length">
      There is nothing to display
    </div>
    <TodoList 
      :todos="filterdTodos" 
      @toggle-todo="toggleTodo" 
      @deleteTodo="deleteTodo"
      />
  </div>
</template>

<script>
import { ref, computed} from 'vue';
import TodoSimpleForm from './components/TodoSimpleForm.vue';
import TodoList from './components/TodoList.vue';

export default {
  components : {
    TodoSimpleForm,
    TodoList,
},

  setup() {
    const todos = ref([]);
    const todoStyle ={
      textDecoration: 'line-through',
      color: 'gray'
    };

    const addTodo = (todo) => {
      console.log(todo);
      //자식이 보낸 데이터 확인
      todos.value.push(todo);
    };

    const toggleTodo = (index) => {
      console.log(todos.value[index]); //변경되기 전
      todos.value[index].completed = !todos.value[index].completed; // 토글 : true -> false, false -> true로 변경해주는 소스
      console.log(todos.value[index]); //변경된 후
    };

    const deleteTodo = (index) => {
      todos.value.splice(index, 1);
    };

    const searchText = ref('');
    const filterdTodos = computed(() => {

      //필터가 된 Array를 넘김
      if(searchText.value) {
        return todos.value.filter(todo => {
          return todo.subject.includes(searchText.value);
        });   
      }

      // 아무것도 없을 시에는 원래 있던 todos를 출력해준다.
      return todos.value;
    });
 
    return {
      todos,
      addTodo,
      todoStyle,
      toggleTodo,
      deleteTodo,
      searchText,
      filterdTodos,
    };
  }
}
</script>

<style>
  .todo {
    color: gray;
    text-decoration: line-through;
  }
</style>