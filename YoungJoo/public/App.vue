<template>
  <!--  
    <div v-show="toggle">true</div>
    <div v-show="!toggle">false</div> 
    -->
  <div v-if="toggle">true</div>
  <div v-else>false</div>
  
  <button @click="onToggle">Toggle</button>
  <div class = "container">

  </div>
</template>

<script>
import { ref } from 'vue';
export default {
  setup(){
      const toggle = ref(false);
      const todo = ref('');
      const todos = ref([
        {id: 1, subject: '휴대폰 사기'},
        {id: 2, subject: '장보기'},
      ]);

      const onSubmit = () => {
        //e.preventDefault(); //refresh하는 속성을 안하게 만듬.
        todos.value.push({
          id: Date.now(), //TimeStamp
          subject: todo.value //제목    
        });
      };

      const onToggle =() =>{
        toggle.value = !toggle.value;
      };


      return {
        todo,
        onSubmit,
        todos,
        toggle,
        onToggle,
      };
  }
}
</script>

<style>

  .name{
    color: red;
  }

</style>
