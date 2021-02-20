<template>
  <v-app>
    <v-app-bar
      absolute
      color="#fcb69f"
      dark
      shrink-on-scroll
      src="./assets/memory.jpg"
      scroll-target="#scrolling-techniques-2"
    >
      <template v-slot:img="{ props }">
        <v-img
          v-bind="props"
          gradient="to top right, rgba(19,84,122,.5), rgba(128,208,199,.8)"
        ></v-img>
      </template>

      <v-app-bar-title class="w-full"
        >Hallo Papa - Ich wünsche Dir alles Gute zum Geburtstag - Ich habe Dich sehr lieb
        <v-icon>mdi-heart</v-icon></v-app-bar-title
      >
    </v-app-bar>

    <v-main class="ma-5">
      <Memory />
    </v-main>
  </v-app>
</template>

<script>
import Memory from './components/Memory';

export default {
  name: 'App',

  components: {
    Memory,
  },

  data: () => ({
    //
  }),
};
</script>

<style>
.v-app-bar-title__content {
  width: 100% !important;
}
</style>
