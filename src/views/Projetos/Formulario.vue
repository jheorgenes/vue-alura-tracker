<template>
  <section>
    <form @submit.prevent="salvar">
      <div class="field">
        <label for="nomeDoProjeto" class="label">
          Nome do Projeto
        </label>
        <input type="text" class="input" v-model="nomeDoProjeto" id="nomeDoProjeto">
      </div>
      <div class="field">
        <button class="button" type="submit">
          Salvar
        </button>
      </div>
    </form>
  </section>
</template>

<script lang="ts">
import { useStore } from '@/store';
import { defineComponent, ref } from 'vue';

import { TipoNotificacao } from '@/interfaces/INotificacao';

import useNotificador from "@/hooks/notificador";
import { ALTERAR_PROJETO, CADASTRAR_PROJETO } from '@/store/tipo-acoes';
import { useRouter } from 'vue-router';

export default defineComponent({
  name: 'Formulario',
  props: {
    id: { type: String }
  },
  setup(props) {
    /**
     * Uma observação importante é que no setup, ainda não existe this (pra nada)
     * Só teria acesso a lógica do this depois que o setup é criado.
     */
    const router = useRouter();
    const store = useStore();
    const { notificar } = useNotificador();

    const nomeDoProjeto = ref(""); //Com isso o nomeDoProjeto se torna uma variável reativa. Para acessar o valor dela deve colocar o .value na frente

    if(props.id) {
      const projeto = store.state.projeto.projetos.find((proj) => proj.id == props.id);
      nomeDoProjeto.value = projeto?.nome || '';
    }

    const ligarComSucesso = () => {
      nomeDoProjeto.value = '';
      notificar(TipoNotificacao.SUCESSO, 'Excelente!', 'O projeto foi cadastrado com sucesso!')
      router.push('/projetos');
    }

    const salvar = () => {
      if(props.id) {
        store
          .dispatch(ALTERAR_PROJETO, { id: props.id, nome: nomeDoProjeto.value })
          .then(() => ligarComSucesso());
      } else {
        store
          .dispatch(CADASTRAR_PROJETO, nomeDoProjeto.value)
          .then(() => ligarComSucesso());
      }
    }

    return {
      nomeDoProjeto,
      salvar
    }
  }
})
</script>

