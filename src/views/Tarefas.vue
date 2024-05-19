<script lang="ts">
  import {computed, defineComponent} from 'vue'
  import Formulario from '@/components/Formulario.vue';
  import Tarefa from '@/components/Tarefa.vue';
  import Box from '@/components/Box.vue';
  import type ITarefa from '@/interfaces/ITarefa';
import { useStore } from '@/store';
import { ADICIONA_TAREFA } from '@/store/tipo-mutacoes';

  export default defineComponent({
    name: 'Tarefas',
    components: {Formulario, Tarefa, Box},
    methods:{
      salvarTarefa(tarefa: ITarefa){
        this.store.commit(ADICIONA_TAREFA, tarefa);
      }
    },
    setup(){
      const store = useStore();
      return{
        store,
        tarefas: computed(() => store.state.tarefas)
      }
    }
  });
</script>

<template>
  <div>
    <Formulario @aoSalvarTarefa="salvarTarefa" />
    <div class="lista">
        <Tarefa v-if="tarefas.length > 0" v-for="(tarefa, index) in tarefas" :key="index" :tarefa="tarefa" />
        <Box v-else>
            Você não está muito produtivo hoje :(
        </Box>
    </div>
  </div>
</template>
