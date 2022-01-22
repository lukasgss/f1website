<template>
  <div class="container">
    <div class="wrapper">
      <div class="container-tempo">
        <!-- COMPONENTE -->
        <TempoProxCorrida />
      </div>
      <div class="container-dados">
        <div class="container-conteudo">
          <img :src="linkLogo" alt="Logo" class="logo" />
          <h1 class="titulo">Bolão Fórmula 1</h1>
          <h3 class="descricao-jogo">Jogo de Bolão das corridas</h3>
          <p class="explicacao-jogo">
            Chute os resultados das corridas e desafie seus amigos para ver quem faz o melhor bolão
          </p>
        </div>
        <div class="container-jogar">
          <a href="#" class="btn btn-jogar"
            >Jogar Bolão Fórmula 1
            <font-awesome class="icone-seta" icon="arrow-right" />
          </a>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import TempoProxCorrida from "./TempoProxCorrida.vue";

export default {
  components: {
    TempoProxCorrida,
  },
  data() {
    return {
      linkLogo: require("./../assets/logo.png"),
    };
  },
};
</script>

<style scoped>
.container {
  background-color: #15151f;
}

.container-tempo {
  padding-top: 25px;
}

.wrapper {
  height: 100vh;
  width: 90%;
  margin: 0 auto;
  color: #fff;
}

.container-dados {
  margin: 0 auto;
  text-align: center;
  padding-top: 20px;
}

.titulo {
  letter-spacing: -2px;
  font-size: 55px;
  font-weight: 900;
}

.logo {
  max-width: 300px;
  margin-bottom: 10px;
}

.container-dados {
  display: flex;
  flex-direction: column;
  justify-content: space-evenly;
  height: 80%;
}

.container-conteudo {
  margin-bottom: 80px;
}

.descricao-jogo {
  font-size: 30px;
  width: fit-content;
  margin: 20px auto;
  padding: 3px;
  border-radius: 2px;
  text-transform: uppercase;
  letter-spacing: 2px;
}

.explicacao-jogo {
  color: rgba(255, 255, 255, 0.71);
  font-size: 25px;
  font-weight: 300;
}

.btn-jogar {
  width: 100%;
  max-width: 320px;
}

.icone-seta {
  margin-left: 5px;
}

.container-jogar {
  height: 100%;
  display: flex;
  align-items: center;
  margin-top: -50px;
  justify-content: center;
}

@media screen and (max-width: 900px) {
}

@media screen and (max-width: 560px) {
  .container-tempo {
    padding-top: 20px;
    margin-bottom: 30px;
  }

  .container-dados {
    padding-top: 0;
  }

  .logo {
    margin: 0;
  }

  .titulo {
    font-size: 42px;
  }

  .descricao-jogo {
    margin: 5px auto;
    font-size: 22px;
  }

  .explicacao-jogo {
    font-size: 20px;
    margin-top: 15px;
  }

  .container-dados {
    justify-content: start;
    margin-top: 15px;
  }

  .container-conteudo {
    margin-bottom: 0px;
  }
}
</style>
