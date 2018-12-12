<template>
<b-container fluid>
    <b-row>
        <b-col cols="12"><input type="text" class="todo-title-input"   v-model="newTitle" placeholder="Введите заголовок задачи" :maxlength="maxTitleLength"></b-col>
        <b-col cols="12"><textarea type="text" class="todo-discription-input" v-model="newTodo" placeholder="Введите задачу"></textarea></b-col>
        <b-col cols="12"><b-button block variant="outline-success" @click="addTodo" class="btn shadow-none">Добавить</b-button></b-col>
        <b-col cols="12">
        <div class="sort-container ">
            <b-button-group size="sm">
            <b-button class="shadow-none"  :class="{ active: filter == 'all' }" @click="applyFilter('all')">Всё</b-button>
            <b-button class="shadow-none" :class="{ active: filter == 'active' }" @click="applyFilter('active')">Активные</b-button>
            <b-button class="shadow-none" :class="{ active: filter == 'completed' }" @click="applyFilter('completed')">Законченные</b-button>
            </b-button-group>
        </div>
        </b-col>
        <b-col cols="12"><div class="check-container"><div>{{ remaining }} не выполненых задач(и)</div>
          <div><label class="sort-label"><input type="checkbox" :checked="!anyRemaining" @change="checkAllTodos"> Выделить все</label></div>
            </div>
        </b-col>
        <b-col cols="12"><input type="text" @change="applyFilter" @blur="applyFilter" class="todo-search-input" v-model="search" placeholder="Поиск по названию"></b-col>
    </b-row>
    <b-row>
      <b-col cols="12">
        <transition-group name="fade" enter-active-class="animated fadeInUp" leave-active-class="animated fadeOutDown">
        <todo-item v-for="(todo,index) in todosFiltered" :key="todo.id" :todo="todo" :index="index" :checkAll="!anyRemaining"
         @removeTodo="removeTodo" @finishedEdit="finishedEdit" >
          
        </todo-item>
         </transition-group>
      </b-col>
    </b-row>
</b-container>
</template>

<script>
import TodoItem from './TodoItem'
export default {
  name: 'todo-list',
  components:{
    TodoItem,
  },
  data (){
    return{
      maxTitleLength: 26,
      newTodo: '',
      newTitle: '',
      beforeEditDiscription: '',
      idForTodo : 2,
      filter: 'all',
      search: '',
      todosFiltered: [],
      todos: [
        {
          'id': 1,
          'title': 'title1',
          'discription': 'text1',
          'completed': false,
          'editing': false,
        },
      ]
    }
  },
  computed:{
    remaining (){
      return this.todos.filter(todo => !todo.completed).length
    },
    anyRemaining(){
      return this.remaining !=0
    },
  },
  methods: {
    addTodo(){
      if (this.newTodo.length == 0 || this.newTitle.length == 0 ){
        return
      }  
      this.todos.push({
        id: this.idForTodo,
        title: this.newTitle,
        discription: this.newTodo,
        completed: false,
      })
      this.newTitle = ''
      this.newTodo = ''
      this.idForTodo++
      this.applyFilter()
    },
    editTodo(todo){
      this.beforeEditDiscription = todo.discription,
      todo.editing = true
      this.applyFilter()
    },
    doneEdit(todo){
      if (todo.discription.length == 0 ){
        todo.discription = this.beforeEditDiscription
      }
      todo.editing = false
      this.applyFilter()
    },
    cancelEdit(todo){
      todo.discription = this.beforeEditDiscription,
      todo.editing = false
    },
    removeTodo(index){
      this.todos.splice(index, 1)
      this.applyFilter()
    },
    checkAllTodos(){
      this.todos.forEach((todo)=>todo.completed=event.target.checked)
    },
    finishedEdit(data){
      this.todos.splice(data.index, 1, data.todo)
      this.applyFilter()
    },
    applyFilter (filter = null) {
      let todosFiltered = [];
      if (filter !== null) {
        this.filter = filter;
      }

      if (this.filter == 'all') {
        todosFiltered = this.todos;
      } else if (this.filter == 'active') {
        todosFiltered = this.todos.filter(todo => !todo.completed);
      } else if (this.filter == 'completed') {
        todosFiltered = this.todos.filter(todo => todo.completed);
      } else {
        todosFiltered = this.todos;
      }
      
      this.todosFiltered = todosFiltered.filter((todo)=>{
        return todo.title.match(this.search);
      })
    },
  },
  mounted (){
    this.todosFiltered = this.todos;
  },
}
</script>


<style scoped>
 @import url("https://cdnjs.cloudflare.com/ajax/libs/animate.css/3.7.0/animate.min.css");

@import url("../main.css");
</style>
