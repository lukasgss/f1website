<template>
  <div class="contador">
    <h2 class="tempo-restante">
      {{ dadosTempoCorridaSeguinte.dias.toString().padStart(2, "0") }}d
      {{ dadosTempoCorridaSeguinte.horas.toString().padStart(2, "0") }}h
      {{ dadosTempoCorridaSeguinte.minutos.toString().padStart(2, "0") }}m
      {{ dadosTempoCorridaSeguinte.segundos.toString().padStart(2, "0") }}s
    </h2>
  </div>
  <div class="texto-contador">
    <p class="texto-tempo">para próxima corrida</p>
  </div>
</template>

<script>
const moment = require("moment");

export default {
  data() {
    return {
      anoAtual: new Date().getUTCFullYear(),
      dataAtual: moment().format("YYYY-MM-DD"),
      dataAtualFormatada: moment().format("DD-MM-YYYY"),
      dadosTempoCorridaSeguinte: {
        dias: "00",
        horas: "00",
        minutos: "00",
        segundos: "00",
      },
    };
  },
  methods: {
    async obterDataProxCorrida() {
      const url = "https://ergast.com/api/f1/current/next.json";
      const resposta = await fetch(url);
      const dadosProxCorrida = await resposta.json();

      const dataProxCorrida = dadosProxCorrida.MRData.RaceTable.Races[0].date;
      const horario = dadosProxCorrida.MRData.RaceTable.Races[0].time.split("Z")[0];
      const arrDataCorrida = dataProxCorrida.split("-");
      let arrHorario = horario.split("Z");
      arrHorario = arrHorario.join("").split(":");

      return {
        dia: arrDataCorrida[2],
        mes: arrDataCorrida[1],
        ano: arrDataCorrida[0],
        hora: arrHorario[0],
        minutos: arrHorario[1],
        segundos: arrHorario[2],
      };
    },
    formatarDadosDiaAtual(dadosHorario, dadosData) {
      const horarioArr = dadosHorario.split(":");
      const dataArr = dadosData.split("/");
      console.log(dadosData);

      return {
        dia: parseInt(dataArr[0]),
        mes: parseInt(dataArr[1]),
        ano: parseInt(dataArr[2]),
        hora: parseInt(horarioArr[0]),
        minutos: parseInt(horarioArr[1]),
        segundos: parseInt(horarioArr[2]),
      };
    },
    async calcularTempoProxCorrida() {
      const dadosProxCorrida = await this.obterDataProxCorrida();
      const horario = new Date().toLocaleTimeString("pt-BR");
      const dataAtual = new Date().toLocaleDateString("pt-BR");
      const dadosDataAtual = this.formatarDadosDiaAtual(horario, dataAtual);

      const dataHoje = new Date(
        dadosDataAtual.ano,
        dadosDataAtual.mes,
        dadosDataAtual.dia,
        dadosDataAtual.hora,
        dadosDataAtual.minutos,
        dadosDataAtual.segundos
      );

      const dataCorrida = new Date(
        dadosProxCorrida.ano,
        dadosProxCorrida.mes,
        dadosProxCorrida.dia,
        dadosProxCorrida.hora,
        dadosProxCorrida.minutos,
        dadosProxCorrida.segundos
      );

      // Subtrai 10.800.000ms (3 horas) por causa do fuso horário brasileiro (GMT -3)
      const milissegundosDataCorrida = dataCorrida.getTime() - 10800000;
      const milissegundosDataHoje = dataHoje.getTime();

      const diferencaDatas = this.calcularDiferencasDatas(milissegundosDataHoje, milissegundosDataCorrida);

      if (diferencaDatas === 0) return;
      this.atualizarTempoRestanteCorrida(diferencaDatas);
    },
    calcularDiferencasDatas(dataAtual, dataCorrida) {
      let diferenca = (dataCorrida - dataAtual) / 1000;
      const diferencaSegundos = Math.floor(diferenca % 60);
      diferenca = Math.floor(diferenca / 60);
      const diferencaMinutos = diferenca % 60;
      diferenca = Math.floor(diferenca / 60);
      const diferencaHoras = diferenca % 24;
      diferenca = Math.floor(diferenca / 24);
      const diferencaDias = diferenca;

      return {
        diferencaSegundos,
        diferencaMinutos,
        diferencaHoras,
        diferencaDias,
      };
    },
    atualizarTempoRestanteCorrida(tempoRestante) {
      this.dadosTempoCorridaSeguinte.dias = tempoRestante.diferencaDias;
      this.dadosTempoCorridaSeguinte.horas = tempoRestante.diferencaHoras;
      this.dadosTempoCorridaSeguinte.minutos = tempoRestante.diferencaMinutos;
      this.dadosTempoCorridaSeguinte.segundos = tempoRestante.diferencaSegundos;
    },
  },
  created() {
    this.calcularTempoProxCorrida();
  },
  mounted: function () {
    window.setInterval(() => {
      if (this.dadosTempoCorridaSeguinte.segundos === 0) {
        this.dadosTempoCorridaSeguinte.segundos = 59;
        this.dadosTempoCorridaSeguinte.minutos--;
      } else {
        this.dadosTempoCorridaSeguinte.segundos--;
      }

      if (this.dadosTempoCorridaSeguinte.minutos === 0 && this.dadosTempoCorridaSeguinte.segundos === 0) {
        this.dadosTempoCorridaSeguinte.segundos = 59;
        this.dadosTempoCorridaSeguinte.minutos = 59;
        this.dadosTempoCorridaSeguinte.horas--;
      }
    }, 1000);
  },
};
</script>

<style scoped>
.container-tempo {
  font-size: 30px;
  padding-top: 20px;
  font-weight: 900;
  margin: 0 auto;
}

.contador {
  text-align: center;
  color: rgb(250, 16, 16);
  font-size: 45px;
}

.texto-contador {
  text-align: center;
  color: rgb(250, 16, 16);
  margin: 0 auto;
  font-size: 35px;
}

.texto-contador {
  margin-top: -5px;
}

@media screen and (max-width: 400px) {
  .contador {
    padding-top: 8px;
    font-size: 37px;
  }

  .texto-contador {
    font-size: 26px;
    margin-top: 0px;
  }
}
</style>
