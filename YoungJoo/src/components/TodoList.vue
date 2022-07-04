<template>
   <div 
      v-for="(todo, index) in todos"
      :key="todo.id"
      class="card mt-2"
    >
      <div class="card-body p-2 d-flex align-items-center">
        <div class="form-check flex-grow-1">
          <input 
            class="form-check-input" 
            type="checkbox"
            :checked="todo.completed"
            @change="toggleTodo(index)"
          >
          <label 
          class="form-check-label"
          :class="{ todo: todo.completed }"
          >
            {{ todo.subject }}
          </label>
        </div>
        <div>
          <button 
            class="btn btn-danger btn-sm"
            @click="deleteTodo(index)"
            >
            Delete
          </button>
        </div>
      </div> 
         
    </div>
</template>

<script>
// import { watchEffect } from 'vue';
export default {
    //데이터 형식 
    //props: ['todos']
    
    //Object 형식
    props: {
        todos: {
            type: Array,
            requeired: true
        }
    },

    empits: ['toggle-todo', 'delete-todo'],

    setup(props, { emit }) {
      // watchEffect(() => {
      //   console.log(props.todos.length)
      // });
        const toggleTodo = (index) => {
            emit('toggle-todo', index);  // 'toggle-todo'는 이벤트 이름
        };

        const deleteTodo = (index) => {
            emit('delete-todo', index);        
        };

        return{
            toggleTodo,
            deleteTodo,
        }
    }

}
</script>

<style>

</style>