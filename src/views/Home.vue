<template>
  <div class="home">
    <PGN
      :pgnFile="pgnGame"
      whitePlayer="TerabyteTiger"
      event="Online Game"
      theme="purple"
      startPosition="4"
    />
  </div>
</template>

<script>
// @ is an alias to /src
import PGN from "@/components/PGN.vue";
// import Chess from "chess.js";

export default {
  name: "Home",
  components: {
    PGN
  },
  data() {
    return {
      // No header info being parsed currently, fails to read pgn when included?
      pgnGame: `1. e4 e5 {king's pawn opening} 2. Nf3 Nc6 3. Bc4 Bc5 {giuoco piano} *`
    };
  }
};
</script>
