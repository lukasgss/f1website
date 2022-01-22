<template>
  <LandingPage />
  <UltimaCorrida />
</template>

<script>
import LandingPage from "./components/LandingPage.vue";
import UltimaCorrida from "./components/UltimaCorrida.vue";

export default {
  name: "App",
  components: {
    LandingPage,
    UltimaCorrida,
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Inter&family=Poppins:wght@300&display=swap");

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

#app {
  font-family: "Inter", sans-serif;
}

.btn {
  padding: 15px 20px;
  background-color: #ff2525;
  color: #fff;
  text-transform: uppercase;
  font-size: 16px;
  text-decoration: none;
  border-radius: 10px;
  font-weight: 900;
}

.btn:hover {
  background: linear-gradient(to right, #fa5a44, #ec1946);
}
</style>
