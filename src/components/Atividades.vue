<template>
  <section class="section container">
    <div class="columns block">
      <div class="column is-8">
        <h2 class="subtitle is-4">
          Nessa aba, você poderá salvar suas atividades realizadas
          em projetos durante a semana do seu expediente.
          Desse modo, tornando mais fácil a citação delas nas reuniões de equipe.
        </h2>
      </div>
      <div class="column is-4 button-add">
        <button
          class="button is-link is-medium is-fullwidth"
          data-target="modal-atividade-add" aria-haspopup="true"
          @click="openModal('modal-atividade-add')" title="Adicionar Atividade"
        >
          Adicionar Atividade
        </button>
        <ModalAddAtiv @criacaoAtividade = 'addAtividade($event)'/>
      </div>
    </div>
    <article class="message is-warning msg-alert" v-show="cardAtividades.length === 0">
      <div class="message-body">
        <p><Icone classes='fas fa-exclamation'/> <b>Nenhuma atividade adicionada</b></p>
      </div>
    </article>
    <div v-show="cardAtividades.length > 0">
      <div class="container div-cards columns is-multiline">
        <CardAtividade
          v-for="card in cardAtividades"
          :key="card.id"
          :dados="card"
          @deletarCard='dltAtividade($event)'
        />
      </div>
      <div class="content-button">
        <button
          class="button is-danger is-outlined"
          title="Apagar toda lista de atividades"
          @click='dltTodasAtividades()'
        >
          <Icone classes='fas fa-trash-alt'/>
          <span>Apagar toda lista</span>
        </button>
      </div>
    </div>
  </section>
</template>

<script>
import ModalAddAtiv from './modais/ModalAddAtividades.vue';
import FunctionModais from './mixins/functionModais';
import CardAtividade from './cards/CardTipo1.vue';
import Icone from './Icones.vue';

export default {
  name: 'Atividades',

  data() {
    return {
      cardAtividades: [],
    };
  },

  mounted() {
    if (localStorage.getItem('atividades')) {
      try {
        this.cardAtividades = JSON.parse(localStorage.getItem('atividades'));
      } catch (error) {
        localStorage.removeItem('atividades');
      }
    }
  },

  mixins: [FunctionModais],

  components: {
    ModalAddAtiv,
    CardAtividade,
    Icone,
  },

  methods: {
    addAtividade($event) {
      this.cardAtividades.push({
        id: Date.now(),
        nomeCampoPrimario: 'Projeto',
        nomeCampoSecundario: 'Atividades Realizadas',
        descricaoPrimario: $event.projeto,
        data: $event.data,
        descricaoSecundario: $event.atividades,
      });
      this.saveAtividadesLS();
    },

    dltAtividade($event) {
      this.cardAtividades = this.cardAtividades.filter((item) => item.id !== $event.id);
      this.saveAtividadesLS();
    },

    dltTodasAtividades() {
      this.cardAtividades = [];
      this.saveAtividadesLS();
    },

    saveAtividadesLS() {
      const parsed = JSON.stringify(this.cardAtividades);
      localStorage.setItem('atividades', parsed);
    },
  },
};
</script>
