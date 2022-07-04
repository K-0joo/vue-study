<template>
<router-view/>
  <div class="container">
    <h2>To-Do List</h2>
     <input
        class="form-control"
        type="text" 
        v-model="searchText"
        placeholder="Search"
        @keyup.enter="searchTodo"
      >
      <hr />
    <TodoSimpleForm @add-todo="addTodo" />
    <div style="color: red">
      {{ error }}
    </div>
 
    <div v-if="!todos.length">
      There is nothing to display
    </div>
    <TodoList 
      :todos="todos" 
      @toggle-todo="toggleTodo" 
      @deleteTodo="deleteTodo"
      />

      <!-- Pagination -->
      <hr />
      <nav aria-label="Page navigation example">
        <ul class="pagination">
          <li v-if="currentPage !== 1" class="page-item">
            <a style="cursor: pointer" class="page-link" @click="getTodos(currentPage - 1)">
            Previous
            </a>
          </li>
          <li
            v-for="page in numberOfPages"
            :key="page"
            class="page-item"
            :class="currentPage === page ? 'actvie' : ''"
            >
            <a style="cursor: pointer" class="page-link" @click="getTodos(page)">{{ page }}</a>
          </li>
          <li v-if="numberOfPages !== currentPage" class="page-item">
            <a style="cursor: pointer" class="page-link"  @click="getTodos(currentPage + 1)">
            Next
            </a>
          </li>
        </ul>
      </nav>
  </div>
</template>

<script>
// @/는 src를 뜻함
import { ref, computed, watch } from 'vue';
import TodoSimpleForm from '@/components/TodoSimpleForm.vue';
import TodoList from '@/components/TodoList.vue';
import axios from 'axios';

export default {
  components: {
    TodoSimpleForm,
    TodoList,
},

  setup() {
    const todos = ref([]); // 초기 값을 빈 배열로 넣고 있음.
    const error = ref('');
    const numberOfTodos = ref(0);
    let limit = 5;
    const currentPage = ref(1);
    const searchText = ref('');

    const numberOfPages = computed(() => {
      return Math.ceil(numberOfTodos.value/limit);
    });

    const getTodos = async (page = currentPage.value) => { // getTodos는 비동기
      currentPage.value = page;
      try{
        // 결과가 올 때 까지 기다린다.
        const res = await axios.get(
          `http://localhost:3000/todos?_sort=id&_order=desc&subject_like=${searchText.value}&_page=${page}&_limit=${limit}` // '' 대신 ``를 쓰면 값을 넣어줄 수 있음.
        ); 
        numberOfTodos.value = res.headers['x-total-count']; // headers에서 x- total-count를 받을 수 있음.
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
        await axios.post('http://localhost:3000/todos', {
          subject: todo.subject,
          completed: todo.completed,
        });

        getTodos(1);
        // todos.value.push(res.data);
      } catch(err){
        console.log(err);
        error.value = 'Somethig went wrong.';
      }
      
      // console.log('hello')
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
        
        getTodos(1);
      } catch (err) {

        console.log(err);
        error.value = 'Something went wrong. ';

      }
    };

    let timeout = null;

    const searchTodo = () => {
      clearTimeout(timeout);
      getTodos(1);
    }

    watch(searchText, () => {
      clearTimeout(timeout); // 기존 2초 타임아웃 초기화
      timeout = setTimeout(()=> { // 2초 대기
        getTodos(1);
      }, 2000)
    });
 
    return {
      searchTodo,
      todos,
      addTodo,
      toggleTodo,
      deleteTodo,
      searchText,
      // filteredTodos,
      error,
      numberOfPages,
      currentPage,
      getTodos,
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