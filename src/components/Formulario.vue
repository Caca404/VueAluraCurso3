<template>
    <div class="box formulario">
        <div class="columns">
            <div class="column is-5" role="form" aria-label="Formulário para criação de uma nova tarefa">
                <input type="text" class="input" placeholder="Qual tarefa você deseja iniciar?" 
                    v-model="descricao" />

            </div>
            <div class="column is-3">
                <div class="select">
                    <select v-model="idProjeto">
                        <option value="">Selecion o Projeto</option>
                        <option v-for="projeto in projetos" :value="projeto.id" :key="projeto.id">
                            {{ projeto.nome }}
                        </option>
                    </select>
                </div>
            </div>
            <div class="column">
                <Temporizador @aoTemporizadorFinalizado="FinalizarTarefa" />
            </div>
        </div>
    </div>
</template>


<script lang="ts">
    import { computed, defineComponent } from 'vue'
    import Temporizador from '@/components/Temporizador.vue';
    import {useStore} from 'vuex';
    import {key} from '@/store';
import { NOTIFICAR } from '@/store/tipo-mutacoes';
import { TipoNotificacao } from '@/interfaces/INotificacao';

    export default defineComponent({
        name: 'Formulário',
        emits: ['aoSalvarTarefa'],
        components:{ Temporizador },
        data(){
            return{
                descricao: '',
                idProjeto: ''
            }
        },
        methods:{
            FinalizarTarefa(tempoDecorrido: number) : void{

                const projeto = this.projetos.find((p) => p.id == this.idProjeto);

                if(!projeto) { // se o projeto não existe...
                    this.store.commit(NOTIFICAR, {
                        titulo: 'Ops!',
                        texto: "Selecione um projeto antes de finalizar a tarefa!",
                        tipo: TipoNotificacao.FALHA,
                    }); // notificamos o usuário
                    return; // ao fazer return aqui, o restante do método salvarTarefa não será executado. chamamos essa técnica de early return :)
                }
                
                this.$emit('aoSalvarTarefa', {
                    duracaoEmSegundos: tempoDecorrido,
                    descricao: this.descricao,
                    projeto: this.projetos.find(proj => proj.id == this.idProjeto)
                });

                this.descricao = '';
            }
        },
        setup(){
            const store = useStore(key);
            
            return {
                store,
                projetos: computed(() => store.state.projetos)
            }
        }
    })
</script>

<style>
    .formulario {
        color: var(--texto-primario);
        background-color: var(--bg-primario);
    }
</style>