<script setup>
import {ref, onMounted, computed, watch} from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

const todos_asc = computed(()=> todos.value.sort((a,b)=>{
  return b.createAt - a.createAt
}))


watch(name, (newVal)=>{
  localStorage.setItem('name',newVal)
})

onMounted(()=>{
  name.value=localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})

const addTodo = ()=>{
  if(input_content.value.trim()=='' || input_category.value ===null){
    return
  }

  todos.value.push({
    content : input_content.value,
    category: input_category.value,
    done:false,
    createdAt: new Date().getTime()
  })

  input_content.value = ''
  input_category.value = null
}

watch(todos, (newVal)=>{
  localStorage.setItem('todos',JSON.stringify(newVal))
},{deep:true})

const removeTodo = todo => {
  todos.value = todos.value.filter(t => t !== todo)
}

</script>


<template>
  <main class="app" style="max-width: 500px ; margin-left: auto; margin-right: auto; margin-top: 100px;">
    <section class="greeting">
      <h2 class="title">
        What's up,<input type="text" placeholder="Name here"
        v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREATE A TODO</h3>

      <form @submit.prevent="addTodo">
        <h4>What's on your todo list ?</h4>
        <input
          type="text" placeholder="e.g. make a video" 
          v-model="input_content" />

        <h4>Pick a category</h4>

        <div class="options">
          <label>
            <input 
              type="radio" 
              name="category"
              value="business"
              v-model="input_category" />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>

          <label>
            <input 
              type="radio" 
              name="category"
              value="personal"
              v-model="input_category" />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        <input type="submit" value="Add todo" />
      </form>
    </section>
    
    <section class="todo-list">
      <h3>Todo List</h3>
      <div class="list">
          <div v-for="todo in todos_asc" 
          :class="`todo-item ${todo.done && 'done'}`" :key="todo" >
            <Label>
              <input type="checkbox" v-model="todo.done" />
              <span :class="`bubble ${todo.category}`"></span>
            </Label>

            <div class="todo-content">
              <input type="text" v-model="todo.content" />
            </div>

            <div class="actions">
              <button class="delete" v-on:click="removeTodo(todo)">Delete</button>
            </div>
          </div>
      </div>
    </section>

    
  </main>
</template>


