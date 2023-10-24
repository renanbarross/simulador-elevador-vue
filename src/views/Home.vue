<script>
import InstrucoesElevador from '@/components/InstrucoesElevador.vue';
import elevatorDingSound from '@/assets/elevator-ding.mp3';

export default {
  components: {
    InstrucoesElevador
  },
  data() {
    return {
      posicaoDoElevador: 0,
      alturaDoAndar: 100,
      solicitacoesAndar: [],
      solicitacoesPainel: [],
      elevadorEmMovimento: false,
      saiuDoRepousoInicial: false,
      contandoCincoSegundos: false,
      recolhido: false,
      interval: null,
      promiseResolve: null
    }
  },

  watch: {
    contandoCincoSegundos: {
      handler(valorNovo, valorAntigo) {
        if (
          valorAntigo == true &&
          valorNovo == false &&
          this.saiuDoRepousoInicial == true &&
          this.solicitacoesAndar.length == 0 &&
          this.solicitacoesPainel.length == 0 &&
          this.recolhido == false
        ) {
          this.movimentarTipo1(1); // volta para o primeiro andar
          this.recolhido = true;
        }
      },
      sync: true
    },
    elevadorEmMovimento: {
      handler(valorNovo, valorAntigo) {
        if (valorAntigo == true && valorNovo == false) {
          this.soarBipe();
        }
      },
      sync: true
    }
  },

  methods: {
    // -------------------- MÉTODOS REFERENTES AOS BOTÕES DOS ANDARES --------------------

    movimentarPeloBotaoDoAndar(numeroAndar) {
      // PARADO:
      if (this.elevadorEmMovimento == false && this.saiuDoRepousoInicial == false && this.contandoCincoSegundos == false) {
        this.movimentarTipo1(numeroAndar);

        // EM MOVIMENTO:
      } else {
        this.movimentarTipo2(numeroAndar);
      }
    },

    async movimentarTipo1(numeroAndar) {
      this.recolhido = false;
      this.solicitacoesAndar.push(numeroAndar); // armazena da fila
      this.moverParaAndar(); // vai até o andar
      await this.esperarPararETerminarCincoSegundos(numeroAndar, this.solicitacoesAndar);
      this.solicitacoesAndar.shift(); // retira da fila
      await this.contarCincoSegundos(); // aguarda 5 segundos
    },

    async movimentarTipo2(numeroAndar) {
      this.recolhido = false;
      this.solicitacoesAndar.push(numeroAndar); // armazena na fila
      await this.esperarPararETerminarCincoSegundos(numeroAndar, this.solicitacoesAndar);
      this.moverParaAndar(); // vai até o andar
      await this.esperarPararETerminarCincoSegundos(numeroAndar, this.solicitacoesAndar);
      this.solicitacoesAndar.shift(); // retira da fila
      await this.contarCincoSegundos(); // aguarda 5 segundos
    },

    // -------------------- MÉTODOS REFERENTES AOS BOTÕES DO PAINEL --------------------

    movimentarPeloBotaoDoPainel(numeroAndar) {
      if (this.elevadorEmMovimento == false && this.saiuDoRepousoInicial == true && this.contandoCincoSegundos == true) {
        this.movimentarTipo3(numeroAndar);
      }
    },

    async movimentarTipo3(numeroAndar) {
      clearInterval(this.interval);
      this.promiseResolve();
      this.recolhido = false;
      this.solicitacoesPainel.push(numeroAndar); // armazena na fila
      this.moverParaAndar(); // vai até o andar
      await this.esperarPararETerminarCincoSegundos(numeroAndar, this.solicitacoesPainel);
      this.solicitacoesPainel.shift(); // retira da fila
      await this.contarCincoSegundos(); // aguarda 5 segundos
    },

    // -------------------- MÉTODOS GERAIS --------------------

    esperarPararETerminarCincoSegundos(numeroAndar, arrayEscolhido) {
      return new Promise(resolve => {
        const interval3 = setInterval(() => {
          if (
            this.elevadorEmMovimento == false &&
            this.contandoCincoSegundos == false &&
            numeroAndar == arrayEscolhido[0] // garante que o andar destino seja o primeiro da fila
          ) {
            clearInterval(interval3);
            resolve();
          }
        }, 0);
      });
    },

    contarCincoSegundos() {
      return new Promise(resolve => {
        this.contandoCincoSegundos = true;
        let segundo = 1;
        this.promiseResolve = resolve;
        this.interval = setInterval(() => {
          if (segundo < 5) {
            segundo++;
          } else {
            this.contandoCincoSegundos = false;
            clearInterval(this.interval);
            resolve();
          }
        }, 1000);
      });
    },

    moverParaAndar() {
      this.elevadorEmMovimento = true;

      if (this.saiuDoRepousoInicial == false) {
        this.saiuDoRepousoInicial = true;
      }

      let posicaoDestino = 0;

      if (this.contandoCincoSegundos == true) {
        posicaoDestino = (this.solicitacoesPainel[0] - 1) * this.alturaDoAndar;
        this.contandoCincoSegundos = false;
      } else {
        posicaoDestino = (this.solicitacoesAndar[0] - 1) * this.alturaDoAndar;
      }
      // Calcula a posição do andar para onde o elevador deve ir

      const diferenca = posicaoDestino - this.posicaoDoElevador;
      // Calcula a diferença entre a posição atual e a posição de destino
      // Se for pra subir será um número positivo
      // Se for pra descer será um número negativo

      const passosAnimacao = 50;

      const tamanhoPasso = diferenca / passosAnimacao;

      let intervaloPasso = 0;

      if (diferenca == 100 | diferenca == -100) {
        intervaloPasso = 50;
      }
      if (diferenca == 200 | diferenca == -200) {
        intervaloPasso = 100;
      }
      if (diferenca == 300 | diferenca == -300) {
        intervaloPasso = 150;
      }

      let passoAtual = 0;

      const animarElevador = setInterval(() => {
        if (passoAtual < passosAnimacao) {
          this.posicaoDoElevador += tamanhoPasso; // faz movimentar
          passoAtual++;
        } else {
          this.posicaoDoElevador = posicaoDestino;
          this.elevadorEmMovimento = false;
          clearInterval(animarElevador);
        }
      }, intervaloPasso);
    },

    soarBipe() {
      console.log("Reproduzindo som...");
      const audio = new Audio(elevatorDingSound);
      audio.play();
    }
  }
}
</script>

<template>
  <div id="conteudo">
    <h1>Elevador</h1>
    <!-- <div>Fila andar: {{ solicitacoesAndar }}</div> -->
    <!-- <div>Fila painel: {{ solicitacoesPainel }}</div> -->
    <!-- <div>Elevador em movimento: {{ elevadorEmMovimento }}</div> -->
    <!-- <div>Contando 5 segundos: {{ contandoCincoSegundos }}</div> -->
    <!-- <div>Saiu do repouso inicial: {{ saiuDoRepousoInicial }}</div> -->
    <div id="layout">
      <InstrucoesElevador />
      <div id="predio">
        <div class="secao">
          <div class="bloco">Andar 4</div>
          <div class="bloco">Andar 3</div>
          <div class="bloco">Andar 2</div>
          <div class="bloco">Andar 1</div>
        </div>

        <div id="poco">
          <div id="elevador" :style="{ bottom: posicaoDoElevador + 'px' }"></div>
        </div>

        <div class="secao">
          <div class="bloco">
            <div class="botao-andar" @click="movimentarPeloBotaoDoAndar(4)">
              <i class="bi bi-arrow-down"></i>
            </div>
          </div>
          <div class="bloco">
            <div class="botao-andar" @click="movimentarPeloBotaoDoAndar(3)">
              <i class="bi bi-arrow-down-up"></i>
            </div>
          </div>
          <div class="bloco">
            <div class="botao-andar" @click="movimentarPeloBotaoDoAndar(2)">
              <i class="bi bi-arrow-down-up"></i>
            </div>
          </div>
          <div class="bloco">
            <div class="botao-andar" @click="movimentarPeloBotaoDoAndar(1)">
              <i class="bi bi-arrow-up"></i>
            </div>
          </div>
        </div>
      </div>
      <div id="painel">
        <div class="secao-painel">
          <div class="botao-painel" @click="movimentarPeloBotaoDoPainel(1)">1</div>
        </div>
        <div class="secao-painel">
          <div class="botao-painel" @click="movimentarPeloBotaoDoPainel(2)">2</div>
        </div>
        <div class="secao-painel">
          <div class="botao-painel" @click="movimentarPeloBotaoDoPainel(3)">3</div>
        </div>
        <div class="secao-painel">
          <div class="botao-painel" @click="movimentarPeloBotaoDoPainel(4)">4</div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
#conteudo {
  height: 100vh;
  width: 50vw;
  display: flex;
  flex-direction: column;
  align-items: center;
}

h1 {
  text-align: center;
}

#layout {
  display: flex;
  flex-direction: row;
  align-items: center;
  margin-top: 30px;
}

#predio {
  height: 400px;
  width: 300px;
  display: flex;
  flex-direction: row;
  border-top: 1px solid black;
  border-bottom: 1px solid black;
}

.secao {
  height: 400px;
  width: 125px;
}

.bloco {
  height: 100px;
  width: 125px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  background-color: grey;
  border: 1px solid black;
  color: white;
}

#poco {
  height: 400px;
  width: 50px;
  display: flex;
  flex-direction: row;
  justify-content: center;
  align-items: end;
  border-top: 1px solid black;
  border-bottom: 1px solid black;
}

#elevador {
  height: 98px;
  width: 46px;
  background-color: brown;
  border: 1px solid black;
  position: relative;
  bottom: 0;
}

#painel {
  height: 200px;
  width: 150px;
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  background-color: #ccc;
  border: 1px solid black;
  margin-left: 80px;
}

.secao-painel {
  width: 75px;
  height: 100px;
  display: flex;
  justify-content: center;
  align-items: center;
}

.botao-painel {
  width: 35px;
  height: 35px;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: #007bff;
  color: white;
  border-radius: 50%;
  border: 1px solid black;
  cursor: pointer;
}

.botao-painel:hover {
  mix-blend-mode: darken;
}

.botao-andar {
  width: 25px;
  height: 25px;
  display: flex;
  justify-content: center;
  align-items: center;
  background-color: rgb(201, 144, 75);
  color: balck;
  border-radius: 30%;
  border: 1px solid black;
  cursor: pointer;
}

.botao-andar:hover {
  mix-blend-mode: darken;
}

i {
  font-size: 15px;
}

h2 {
  color: black;
  margin: 0 10px 0;
}

h2>i {
  font-size: 20px;
  margin-right: 10px;
}
</style>