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
        <div v-for="(todo,index) in todosFiltered" :key="todo.id" class="todo-item">
          <div class="card-properties">
            <b-card no-body :class="{completedcard: todo.completed}">
              <b-card-header class="element-container">    
                <h5 class="card-text" :class="{completedtext: todo.completed}">{{todo.title}}</h5>
                <input type="checkbox" v-model="todo.completed" > 
                </b-card-header>          
                  <b-card-body >
                    <div>
                      <pre class="card-discription-style" v-if="!todo.editing" @dblclick="editTodo(todo)" :class="{completedtext: todo.completed}">{{todo.discription}}</pre>
                      <textarea class="discription-edit" v-else type="text" v-model="todo.discription" v-focus></textarea>
                    </div>  
                  </b-card-body>
                <b-card-footer class="footer element-container" >
                  <b-button-group size="sm">
                  <b-button variant="outline-success" class="remove-item shadow-none" @click="doneEdit(todo)">Сохранить</b-button>
                  <b-button variant="outline-info" class="remove-item shadow-none" @click="cancelEdit(todo)">Отменить</b-button>
                  </b-button-group>
                  <b-button variant="outline-danger" class="remove-item" @click="removeTodo(index)">&times;</b-button>
                </b-card-footer>
            </b-card>
          </div>  
        </div>
         </transition-group>
      </b-col>
    </b-row>
</b-container>
</template>

<script>
export default {
  name: 'todo-list',
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
  directives: {
    focus: {
      inserted: function (el) {
        el.focus()
      }
    }
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

.todo-discription-input,.todo-title-input,.todo-search-input {
  width: 100%;
  padding:  8px 8px;
  font-size: 16px;
  margin-bottom: 10px;
}
.card-properties{
  margin: 8px 0px;
}
.header-title{
  margin-bottom: 0px;
}
.remove-item{
  cursor: pointer;
  padding: 2px 8px;

}
.item-left{
  display:flex;
  align-items: center;
}
.card-text{
  padding: 10px 0px;
  margin-bottom: 0px;
}
.card-discription-style{
  font-size: 16px;
  margin-bottom: 0px;
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
}
.discription-edit{
  font-size: 16px;
  width: 100%;
  padding: 10px 0px;
  border: 1px solid #ccc;
}
.completedcard {
  background-color: rgba(109, 108, 108, 0.212);
  
}
.completedtext{
  text-decoration: line-through;
  color: rgba(109, 108, 108, 0.404);
}
.element-container {
    display: flex;
    align-items: center;
    justify-content: space-between;    
}
.check-container {
    display: flex;
    align-items: center;
    justify-content: space-between;
    border: 1px solid lightgrey;
    padding: 8px 8px;
    margin: 8px 0px;   
}
.sort-container {
    display: flex;
    align-items: center;
    justify-content: center;
    border: 1px solid lightgrey;
    padding: 8px 8px;
    margin-top: 8px;   
}
.sort-label{
  margin-bottom:  0px;
}
</style>
