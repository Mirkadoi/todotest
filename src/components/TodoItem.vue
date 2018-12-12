<template>
    <div class="todo-item" >
          <div class="card-properties">
            <b-card no-body :class="{completedcard: completed}">
              <b-card-header class="element-container">    
                <h5 class="card-text" :class="{completedtext: completed}">{{title}}</h5>
                <input type="checkbox" v-model="completed" @change="doneEdit"> 
                </b-card-header>          
                  <b-card-body >
                    <div>
                      <pre class="card-discription-style" v-if="!editing" @dblclick="editTodo" :class="{completedtext: completed}">{{discription}}</pre>
                      <textarea class="discription-edit" v-else type="text" v-model="discription" ></textarea>
                    </div>  
                  </b-card-body>
                <b-card-footer class="element-container" >
                  <b-button-group size="sm">
                  <b-button variant="outline-success" class="remove-item shadow-none" @click="doneEdit">Сохранить</b-button>
                  <b-button variant="outline-info" class="remove-item shadow-none" @click="cancelEdit">Отменить</b-button>
                  </b-button-group>
                  <b-button variant="outline-danger" class="remove-item" @click="removeTodo(index)">&times;</b-button>
                </b-card-footer>
            </b-card>
          </div> 
    </div>
</template>

<script>
    export default {
    neme: 'todo-item',
    props: {
        todo:{
            type: Object,
            required: true,
        },
        index:{
            type: Number,
            required: true,
        },
        checkAll:{
            type: Boolean,
            required:true,
        },
    },
    data(){
        return{
            'id':this.todo.id,
            'title': this.todo.title,
            'discription': this.todo.discription,
            'completed': this.todo.completed,
            'editing': this.todo.editing,
            'beforeEditDiscription': '',
        }
    },
    watch:{
        checkAll(){
            if(this.checkAll){
                this.completed = true;
            }
            else{
                this.completed = this.todo.completed;
            }
        }
    },
    methods: {
        removeTodo(index){
            this.$emit('removeTodo',index)
        },
        editTodo(){
            this.beforeEditDiscription = this.discription,
            this.editing = true
        },
        doneEdit(){
            if (this.discription.length == 0 ){
            this.discription = this.beforeEditDiscription
        }
            this.editing = false
            this.$emit('finishedEdit',{
                'index': this.index,
                'todo': {
                    'id':this.id,
                    'title': this.title,
                    'discription': this.discription,
                    'completed': this.completed,
                    'editing': this.editing,
                }
            })
        },
        cancelEdit(){
        this.discription = this.beforeEditDiscription,
        this.editing = false
        }, 
    }  
}

</script>

<style scoped>

</style>