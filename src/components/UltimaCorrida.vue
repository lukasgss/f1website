<template>
  <div class="container-tabela">
    <div class="wrapper-tabela">
      <div v-if="carregando" class="carregando">
        <h2 class="texto-carregando">Carregando...</h2>
      </div>
      <div v-else class="tabela">
        <h2 class="titulo-tabela">
          Última corrida <span class="traco-separacao">-</span> Temporada {{ dadosUltimaCorrida.temporada }}
        </h2>
        <h2 class="subtitulo-tabela">Rodada {{ dadosUltimaCorrida.rodada }}</h2>

        <div class="container-table">
          <h2 class="titulo-corrida">{{ dadosUltimaCorrida.circuito }}, {{ dadosUltimaCorrida.localizacao }}</h2>
          <table class="tabela">
            <thead>
              <tr>
                <th
                  v-for="(dados, index) in dadosTabela.colunasTabela"
                  :key="index"
                  :class="dados.classe"
                  class="dados-exibidos"
                >
                  {{ dados.nomeColuna }}
                </th>
              </tr>
            </thead>
            <tbody>
              <!-- TODO: Validar os casos onde dados.Time.time não existe (trocar tudo que não for
              tempo ou retardatário por DNF) -->
              <tr v-for="dados in dadosUltimaCorrida.dadosPosicoes" :key="dados.number" class="dados-tabela">
                <td class="posicao-piloto">{{ dados.position }}</td>
                <td class="numero-piloto">{{ dados.number }}</td>
                <td class="nome-piloto">{{ dados.Driver.givenName }} {{ dados.Driver.familyName }}</td>
                <td class="construtor-piloto">{{ dados.Constructor.name }}</td>
                <td v-if="dados.Time" class="tempo-piloto">{{ dados.Time.time }}</td>
                <td v-else class="tempo-piloto">{{ dados.status }}</td>
                <td class="voltas-piloto">{{ dados.laps }}</td>
                <td class="pontos-piloto">{{ dados.points }}</td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      dadosTabela: {
        colunasTabela: [
          { nomeColuna: "Posição", classe: "coluna-posicao" },
          { nomeColuna: "Número", classe: "coluna-numero" },
          { nomeColuna: "Piloto", classe: "coluna-piloto" },
          { nomeColuna: "Carro", classe: "coluna-carro" },
          { nomeColuna: "Tempo", classe: "coluna-tempo" },
          { nomeColuna: "Voltas", classe: "coluna-voltas" },
          { nomeColuna: "Pts", classe: "coluna-pts" },
        ],
      },
      colunasTabela: ["Posição", "Número", "Piloto", "Carro", "Voltas", "Pts"],
      dadosUltimaCorrida: {
        tempoPilotos: [],
      },
      carregando: true,
    };
  },
  methods: {
    async obterDadosUltimaCorrida() {
      const urlApi = "https://ergast.com/api/f1/current/last/results.json";
      const resposta = await fetch(urlApi);
      const dados = await resposta.json();

      return dados.MRData.RaceTable.Races[0];
    },
    async preencherTabelaUltimaCorrida() {
      const dadosCorrida = await this.obterDadosUltimaCorrida();

      const dadosTabela = {
        circuito: dadosCorrida.Circuit.circuitName,
        localizacao: dadosCorrida.Circuit.Location.locality,
        rodada: dadosCorrida.round,
        temporada: dadosCorrida.season,
        dadosCorrida: dadosCorrida,
        dadosPosicoes: dadosCorrida.Results,
      };

      // A partir do momento em que os dados chegam nos DNFs, não há mais o atributo
      // Time.time, então é passado a usar o "status" para indicar que são DNFs, então
      // é alterado o motivo do DNF retornado (ex.: accident) para DNF
      function formatarTempoTabela() {
        for (let i = 0; i < dadosTabela.dadosCorrida.Results.length; i++) {
          if (
            !dadosTabela.dadosCorrida.Results[i].status.includes(":") &&
            !dadosTabela.dadosCorrida.Results[i].status.includes("+")
          )
            dadosTabela.dadosCorrida.Results[i].status = "DNF";
        }
      }

      formatarTempoTabela();
      Object.assign(this.dadosUltimaCorrida, dadosTabela);

      this.carregando = false;
    },
  },
  created() {
    this.preencherTabelaUltimaCorrida();
  },
};
</script>

<style scoped>
.wrapper-tabela {
  width: 89%;
  margin: 0 auto;
}

.tabela {
  margin-top: 5px;
}

.titulo-tabela {
  text-align: center;
  font-size: 28px;
  text-transform: uppercase;
  letter-spacing: 2px;
  margin-top: 15px;
}

.subtitulo-tabela {
  text-align: center;
  margin-top: 14px;
  text-transform: uppercase;
  font-size: 21px;
}

.titulo-corrida {
  margin: 10px 0 0;
  font-size: 18px;
  padding-left: 3px;
  color: gray;
}

.container-table {
  margin: 20px auto;
  margin-bottom: 20px;
  width: fit-content;
}

table {
  margin: 0 auto;
  text-align: center;
  border-collapse: collapse;
  min-width: 300px;
  border-radius: 5px 5px 0 0;
  overflow: hidden;
  box-shadow: 0 0 20px rgba(0, 0, 0, 0.15);
}

table thead tr {
  font-weight: 900;
  background-color: #f72a2a;
  color: #fff;
}

table th,
table td {
  padding: 12px 15px;
}

.dados-tabela {
  border-bottom: 1px solid #ddd;
}

table tbody tr:nth-of-type(odd) {
  background-color: #f3f3f3;
}

table tbody tr:last-of-type {
  border-bottom: 2px solid #f72a2a;
}

@media screen and (max-width: 528px) {
  .traco-separacao {
    display: none;
  }
}

@media screen and (max-width: 790px) {
  .voltas-piloto,
  .coluna-voltas {
    display: none;
  }

  .wrapper-tabela {
    width: 90%;
  }
}

@media screen and (max-width: 650px) {
  .numero-piloto,
  .coluna-numero {
    display: none;
  }
}

@media screen and (max-width: 550px) {
  .construtor-piloto,
  .coluna-carro {
    display: none;
  }
}

@media screen and (max-width: 430px) {
  .coluna-tempo,
  .tempo-piloto {
    display: none;
  }

  .wrapper-tabela {
    width: 100%;
  }
}
</style>
