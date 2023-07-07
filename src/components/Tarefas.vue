<template>
    <div>
        <h1 class="display-4 text-center">Lista de tarefas</h1>
        <hr>
        <div class="row">
            <div class="col-lg-8 offset-lg-2">
                <div class="card mt-4">
                    <div class="card-body">
                        <div class="input-group">
                            <input type="text" v-model="listaTarefas"
                                   class="form-control form-control-lg" placeholder="Adicionar Tarefa">
                            <div class="input-group-append">
                               <button v-on:click="addTrefas()"
                               class="btn btn-success btn-lg">
                               Adicionar
                                </button>     
                            </div>
                        </div>
                        <br>
                        <div class="text-center">
                            <div v-if="loading" class="spinner-border text-success" role="status">
                            <span class="sr-only">Loading...</span>
                            </div>
                        </div>
                        <h5 v-if="conjutoTrefas.length == 0">Não há tarefas</h5>
                        <ul v-if="!loading" class="list-group">
                            <li v-for="(listaTarefas,index) of conjutoTrefas" :key="index"
                                class="list-group-item d-flex justify-content-between">
                                <span class="cursor" 
                                      v-bind:class="{'text-success':listaTarefas.estado}"
                                      v-on:click="editarTarefas(listaTarefas,listaTarefas.id)">
                                    <i 
                                     v-bind:class="[listaTarefas.estado ? 'fas fa-check-circle' : 'far fa-circle']"></i>
                                </span>
                                {{ listaTarefas.nome }}
                                <span class="text-danger cursor" v-on:click="excluirTarefa(listaTarefas.id)">
                                    <i class="fas fa-trash-alt"></i>
                                </span>
                            </li>
                        </ul>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from "axios";

export default {
    name: 'ListaTarefas',
    mounted() {
    this.obterTarefas();
  },
    data(){
        return{
            listaTarefas:'',
            conjutoTrefas:[],
            loading:false,
        }
    },
    methods:{
        addTrefas(){
            const listaTarefas = {
                nome:this.listaTarefas,
                estado:false
            }
            // this.conjutoTrefas.push(listaTarefas);
            this.loading = true;
            axios.post("https://localhost:7158/api/Tarefa/", listaTarefas).then(response => {
                console.log(response);
                this.loading = false;
                this.obterTarefas();
            }).catch(error =>{
                console.error(error);
                this.loading = false;
            })
            this.listaTarefas = '';
        },
        excluirTarefa(id){
            this.loading = true;
        axios.delete("https://localhost:7158/api/Tarefa/" + id).then(response =>{
            console.log(response.data);
            this.obterTarefas();
            this.loading = false;
        }).catch(erro => {
            console.log(erro);
            this.loading = false;
        });
      },
      editarTarefas(listaTarefas,id){
        // this.conjutoTrefas[index].estado = !listaTarefas.estado
        this.loading = true;
        axios.put("https://localhost:7158/api/Tarefa" + id, listaTarefas).then(()=>{
            this.obterTarefas();
            this.loading = false
        }).catch(()=>this.loading = false);
      },
      obterTarefas(){
        this.loading = true;
        axios.get("https://localhost:7158/api/Tarefa").then(response =>{
            
            console.log(response.data);
            this.conjutoTrefas = response.data;
            this.loading = false;
        }).catch(()=>this.loading = false);
      },
    created:function(){
        this.obterTarefas();
    }

}
}
</script>

<style scoped>
.cursor{
    cursor:pointer;
}
</style>