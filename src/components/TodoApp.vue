<script setup>

  import { computed, onMounted, ref, watch } from 'vue';

  //使用者輸入的代辦事項
  const newTodo = ref('') 

  //使用者輸入的修改內容
  const newTaskName = ref('') 

  //放置代辦事項的Array
  const todoList = ref([]) 

  //顯示內容的狀態
  const filter = ref('all')

  //搜尋框內容
  const searchInput = ref('')

  const handleSubmit = () => {
    //如果使用者輸入的內容去掉頭尾空白後的長度是0，跳出alert，沒有的話就放進Array
    if(newTodo.value.length === 0) {
      alert('Please enter something')
      return
    }

    const newTodoObj = {
      id: todoList.value.length === 0 ? 0 : todoList.value[0].id + 1,
      taskName: newTodo.value,
      done: false,
      isEditing: false,
    }
    todoList.value = [newTodoObj, ...todoList.value]

    //清空input的值
    newTodo.value = ''
  }

  const toggleComplete = (todoId) => {
    todoList.value.forEach(todo => {
      if(todo.id === todoId) {
        todo.done = !todo.done
      }
    })
  }

  const toggleEditing = (todoId) => {
    todoList.value.forEach(todo => {
      if(todo.id === todoId) {
        todo.isEditing = !todo.isEditing
      }
    })
    newTaskName.value = ''
  }

  const deleteTodo = (todoId) => {
    todoList.value = todoList.value.filter((todo) => todo.id !== todoId)
  }

  const handleSave = (todoId) => {
    if(!newTaskName.value.length){
      alert('Please enter something')
      return
    } 
    todoList.value.forEach(todo => {
      if(todo.id === todoId) {
        todo.taskName = newTaskName.value
        todo.isEditing = !todo.isEditing
      }
    })
    newTaskName.value = ''
  }

  const filteredTodos = computed(() => {
    if(searchInput.value === 0) return
    if (filter.value === 'all') {
      return todoList.value.filter(todo => todo.taskName.toLowerCase().includes(searchInput.value.toLowerCase()))
    } 
    else if (filter.value === 'completed') {
      return todoList.value.filter(todo => todo.done && todo.taskName.toLowerCase().includes(searchInput.value.toLowerCase()));
    } 
    else if (filter.value === 'incomplete') {
      return todoList.value.filter(todo => !todo.done && todo.taskName.toLowerCase().includes(searchInput.value.toLowerCase()));
    }
  })

  watch(todoList,() => {
    localStorage.setItem('todos', JSON.stringify({todo: todoList.value, filter: filter.value}))
  },{deep:true})

  watch(filter,() => {
    localStorage.setItem('todos', JSON.stringify({todo: todoList.value, filter: filter.value}))
  })

  onMounted(() => {
    resumeTodos()
  })
  function resumeTodos() {
    const todos = JSON.parse(localStorage.getItem('todos'))
    todoList.value = todos.todo
    filter.value = todos.filter
  }
  

</script>

<template>

  <div class="todo-app">

    <!-- 輸入與新增 -->
    <div class="todo-input">
      <form @submit.prevent="handleSubmit">
        <input type="text" v-model.trim="newTodo">
        <button class="add-btn">Add</button>
      </form>
      <select v-model="filter">
        <option value="all">All</option>
        <option value="completed">Completed</option>
        <option value="incomplete">Incomplete</option>
      </select>
      <input type="text" placeholder="search ..." v-model.trim="searchInput" >
    </div>

    <!-- todo list -->
    <div class="todo-list">

      <div v-for="todo in filteredTodos" :key="todo.id" >
        <div v-if="!todo.isEditing" class="todo">
          <p :class="{done: todo.done}">{{ todo.taskName }}</p>
          <i class="fa-regular fa-pen-to-square" @click="toggleEditing(todo.id)"></i>
          <i class="fa-solid fa-check" @click="toggleComplete(todo.id)" style="color: #42b883;"></i>
          <i class="fa-solid fa-trash-can" @click="deleteTodo(todo.id)" style="color:#e46161"></i>
        </div>
        <form v-if="todo.isEditing" @submit.prevent class="edit-mode">
          <div class="edit-form">
            <input type="text" v-model.trim="newTaskName">
            <i class="fa-regular fa-square-check" @click="handleSave(todo.id)"></i>
            <i class="fa-solid fa-arrow-right-from-bracket" @click="toggleEditing(todo.id)"></i>
          </div>
        </form>
      </div>
    </div>
  </div>

</template>

<style scoped>

.todo-list {
  margin: 50px 0;
  min-width: 350px;
}

/* 輸入與新增 */
  form {
    display: flex;
    height: 50px;
  }
  form input {
    flex: 1;
  }
  input[type='text'] {
    outline: 0;
    border: 1px solid #ccc;
    background-color: hsla(0, 0%, 98%, 0.687);
    height: 100%;
    padding: 0 1rem;
  }
  .add-btn{
    border: 2px solid #24b4fb;
    background-color: #24b4fb;
    color: #e3e3e3;
    border-radius: 0 0.9em 0.9em 0;
    padding: 0.8em 1.2em 0.8em 1em;
    height: 100%;
    transition: all ease-in-out 0.2s;
    font-size: 16px;
    cursor: pointer;
    }
  .add-btn:hover {
    background-color: #0071e2;
  }

  /* todo list */
  .todo {
    display: flex;
    justify-content: space-between;
    align-items: center;
    height: 50px;
    margin: 1rem 0;
    padding: 0 5px;
    color: hsla(0, 0%, 98%, 0.687);
    border: 1px solid #ccc;
  }
  .todo p {
    flex: 1;
    font-size: 1.25rem;
  }
  .todo i {
    height: 100%;
    margin-left: 10px;
    cursor: pointer;
    line-height: 50px;
    transition: transform 0.2s;
  }
  .todo i:hover {
    transform: rotate(-15deg);
  }


  /* edit mode */
  .edit-mode {
    margin: 1rem 0;
  }
  .edit-form {
    display: flex;
    justify-content: space-between;
    align-items: center;
    height: 50px;
    width: 100%;
    padding: 0 5px;
    border: 1px solid #ccc;
    color: hsla(0, 0%, 98%, 0.687);
  }
  .edit-form input {
    flex: 1;
    background-color: transparent;
    outline: 0;
    border: 0;
    padding: 0;
    color: hsla(0, 0%, 98%, 0.687);
    font-size: 1rem;
  }
  .edit-form i {
    line-height: 50px;
    cursor: pointer;
    margin-left: 10px;
  }
  .done {
    text-decoration: line-through;
    color:hsla(0, 0%, 98%, 0.687);
    opacity: 0.5;
  }

</style>