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
    <div style="color: red">
      {{ error }}
    </div>
 
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
import axios from 'axios';

export default {
  components : {
    TodoSimpleForm,
    TodoList,
},

  setup() {
    const todos = ref([]); // 초기 값을 빈 배열로 넣고 있음.
    const error = ref('');

    const getTodos = async () => { // getTodos는 비동기
      try{
        // 결과가 올 때 까지 기다린다.
        const res = await axios.get('http://localhost:3000/todos') // 모든 todos 데이터를 요청함.    
        todos.value = res.data; // 결과
      } catch(err) {
        console.log(err); // 에러
        error.value = 'Somethig went wrong.';
      }
    };

    // 위 함수 실행 시킴
    getTodos();

    const addTodo = async (todo) => {
      error.value = '';
      try{
        const res = await axios.post('http://localhost:3000/todos', {
          subject: todo.subject,
          completed: todo.completed,
        });
        todos.value.push(res.data);
      } catch(err){
        console.log(err);
        error.value = 'Somethig went wrong.';
      }
      
      console.log('hello')
    };

    const toggleTodo = async (index) => {
      error.value = '';
      const id = todos.value[index].id;

      try{
        await axios.patch('http://localhost:3000/todos/' + id, {
          completed: !todos.value[index].completed
        });    

        todos.value[index].completed = !todos.value[index].completed;

      } catch(err){

        console.log(err);
        error.value = 'Something went wrong. ';
        
      }
    };

    const deleteTodo = async (index) => {
      error.value = ''; //오류 메세지 초기화
      const id = todos.value[index].id;

      try {

        await axios.delete('http://localhost:3000/todos/' + id);
        todos.value.splice(index, 1); // await 성공 시 실행 실패시 catch로 감

      } catch (err) {

        console.log(err);
        error.value = 'Something went wrong. ';

      }
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
      toggleTodo,
      deleteTodo,
      searchText,
      filterdTodos,
      error,
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