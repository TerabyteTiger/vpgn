<template>
  <div :class="theme">
    <div class="btn-flex">
      <button @click="flipBoard">
        Flip Board
      </button>
    </div>
    <!-- Add Header info here -->
    <div class="chessheader">
      <b>{{ whitePlayer }} vs. {{ blackPlayer }}</b>
      <br />
      <p>{{ event }}</p>
    </div>
    <div class="flex-board">
      <div class="board">
        <div class="row" v-for="r in 8" :key="r">
          <div class="square file-label">
            {{
              ["8", "7", "6", "5", "4", "3", "2", "1"][
                r - 1
              ]
            }}&nbsp;
          </div>
          <div
            class="square"
            v-for="c in 8"
            :key="c"
            v-bind:class="[
              (c & 1 && r & 1) || (!(c & 1) && !(r & 1))
                ? 'white'
                : 'black',
              getPiece(r, c),
              'piece'
            ]"
          ></div>
        </div>
        <div class="row">
          <div class="square"></div>
          <div class="square text-center">a</div>
          <div class="square text-center">b</div>
          <div class="square text-center">c</div>
          <div class="square text-center">d</div>
          <div class="square text-center">e</div>
          <div class="square text-center">f</div>
          <div class="square text-center">g</div>
          <div class="square text-center">h</div>
        </div>
      </div>
      <div class="notation">
        <div
          v-for="(moveRow, index) in Math.ceil(
            moves.length / 2
          )"
          :key="index"
        >
          {{ moveRow }}.
          <span
            class="pointer"
            :class="
              currentMove == index * 2 + 1
                ? 'highlight'
                : ''
            "
            @click="goToMove(2 * index + 1)"
            >{{ moves[2 * index] }}</span
          >
          &nbsp;
          <span
            class="pointer"
            @click="goToMove(2 * index + 2)"
            :class="
              currentMove == index * 2 + 2
                ? 'highlight'
                : ''
            "
            >{{
              moves.length > index + 1
                ? moves[2 * index + 1]
                : ""
            }}</span
          >
        </div>
      </div>
    </div>

    <div class="btn-flex">
      <button @click="flipBoard">Flip Board</button>
      <button @click.prevent="goToMove(0)">
        |&lt;&lt;
      </button>
      <button @click.prevent="prevMove()">
        &lt;
      </button>
      <button @click.prevent="nextMove()">&gt;</button>
      <button @click.prevent="goToMove(moves.length)">
        &gt;&gt;|
      </button>
    </div>
    <br />
    <p
      class="comments"
      v-if="Object.entries(comments).length !== 0"
    >
      {{ comments[game.fen()] }}
    </p>
  </div>
</template>

<script>
import Chess from "chess.js";
// Major Thanks to https://github.com/deemaagog/vue-pgn
// for functioning as a guideline to piece together the last bits of how chess.js works
export default {
  data() {
    return {
      game: null,
      moves: [],
      position: [],
      currentMove: 0,
      comments: {},
      isBoardFlipped: false
    };
  },
  props: {
    pgnFile: {
      type: String,
      default: ""
    },
    startPosition: {
      default: 0
    },
    whitePlayer: {
      type: String,
      default: "Unknown"
    },
    blackPlayer: {
      type: String,
      default: "Unknown"
    },
    event: {
      type: String,
      default: ""
    },
    theme: {
      type: String,
      default: "standard"
    }
  },
  watch: {
    pgnFile: function() {
      this.loadPgn();
    }
  },
  created() {
    this.game = new Chess();
  },
  mounted() {
    this.loadPgn();
    this.currentMove = this.startPosition;
  },
  methods: {
    getPiece(row, col) {
      if (this.position.length == 0) return null;
      let piece;
      if (this.isBoardFlipped) {
        piece = this.position[9 - row - 1][col - 1];
      } else {
        piece = this.position[row - 1][col - 1];
      }
      return piece === null ? "" : piece.color + piece.type;
    },
    loadPgn() {
      this.game.load_pgn(this.pgnFile);
      this.moves = this.game.history();
      this.comments = this.setComments();
      this.game.reset();
      this.position = this.game.board();
      this.goToMove(this.startPosition);
    },
    goToMove(moveNo) {
      if (moveNo > 0 && moveNo < this.moves.length + 1) {
        this.game.reset();
        for (var i = 0; i < moveNo; i++) {
          this.game.move(this.moves[i]);
        }
        this.position = this.game.board();
        this.currentMove = moveNo;
      }
      if (moveNo === 0) {
        this.game.reset();
        this.position = this.game.board();
        this.currentMove = 0;
      }
    },
    nextMove() {
      this.currentMove = parseInt(this.currentMove) + 1;
      if (this.currentMove >= this.moves.length) {
        this.currentMove = this.moves.length;
      }
      this.goToMove(this.currentMove);
    },
    prevMove() {
      this.currentMove = this.currentMove - 1;
      if (this.currentMove <= 0) {
        this.currentMove = 0;
      }
      this.goToMove(this.currentMove);
    },
    setComments() {
      let commentsArray = this.game.get_comments();
      let commentsObj = {};
      commentsArray.forEach(el => {
        commentsObj[el.fen] = el.comment;
      });
      return commentsObj;
    },
    flipBoard() {
      this.isBoardFlipped = !this.isBoardFlipped;
      this.loadPgn();
    }
  }
};
</script>

<style scoped>
/* Themes */
.purple {
  --darkSquares: hsl(237, 51%, 73%);
  --lightSquares: hsl(235, 100%, 95%);
}

.standard {
  --darkSquares: hsl(32, 53%, 59%);
  --lightSquares: hsl(32, 76%, 77%);
}

.pink {
  --darkSquares: hsl(298, 66%, 64%);
  --lightSquares: hsl(290, 76%, 77%);
}

.green {
  --darkSquares: hsl(100, 66%, 64%);
  --lightSquares: hsl(100, 76%, 77%);
}

.red {
  --darkSquares: hsl(0, 66%, 64%);
  --lightSquares: hsl(0, 76%, 77%);
}

.gray {
  --darkSquares: hsl(0, 0%, 50%);
  --lightSquares: hsl(0, 0%, 75%);
}

.grey {
  --darkSquares: hsl(0, 0%, 50%);
  --lightSquares: hsl(0, 0%, 75%);
}

.blue {
  --darkSquares: hsl(200, 66%, 64%);
  --lightSquares: hsl(200, 76%, 77%);
}

.muted {
  --darkSquares: hsl(198, 21%, 68%);
  --lightSquares: hsl(198, 22%, 82%);
}

.btn-flex {
  display: flex;
  justify-content: space-between;
  width: 400px;
  margin: 10px auto;
}

.btn-flex button {
  font-size: 1.25rem;
  padding: 0.25rem 0.75rem;
}

.flex-board {
  display: flex;
  justify-content: center;
}
.notation {
  width: 150px;
  max-height: 355px;
  overflow-y: auto;
  text-align: left;
  padding-left: 15px;
}

.board {
  flex-grow: 0;
  flex-shrink: 0;
  position: relative;
  height: 400px;
  width: 400px;
}

.black {
  background-color: var(--darkSquares);
}

.white {
  background-color: var(--lightSquares);
}

.square {
  width: 11.11%;
  height: 100%;
  float: left;
}

.row {
  height: 11.11%;
}

.piece {
  background-repeat: no-repeat;
  background-position: center;
}

.wp {
  background-image: url("../assets/svgs/wp.svg");
  background-size: 55%;
}
.wq {
  background-image: url("../assets/svgs/wq.svg");
  background-size: 80%;
}
.wk {
  background-image: url("../assets/svgs/wk.svg");
  background-size: 70%;
}
.wn {
  background-image: url("../assets/svgs/wn.svg");
  background-size: 75%;
}
.wb {
  background-image: url("../assets/svgs/wb.svg");
  background-size: 75%;
}
.wr {
  background-image: url("../assets/svgs/wr.svg");
  background-size: 65%;
}

.bp {
  background-image: url("../assets/svgs/bp.svg");
  background-size: 55%;
}
.bq {
  background-image: url("../assets/svgs/bq.svg");
  background-size: 80%;
}
.bk {
  background-image: url("../assets/svgs/bk.svg");
  background-size: 70%;
}
.bn {
  background-image: url("../assets/svgs/bn.svg");
  background-size: 75%;
}
.bb {
  background-image: url("../assets/svgs/bb.svg");
  background-size: 75%;
}
.br {
  background-image: url("../assets/svgs/br.svg");
  background-size: 65%;
}

.highlight {
  background-color: #fff365;
}
span {
  padding: 2px;
}

.chessheader {
  font-size: 1.2rem;
  padding: 7px;
}

.comments {
  min-height: 100px;
  overflow-y: auto;
  border: 1px solid black;
  width: 70%;
  margin: auto;
  padding: 7px;
  text-align: left;
  margin-bottom: 1rem;
}

.file-label {
  display: flex;
  justify-content: right;
  align-items: center;
}

.text-center {
  text-align: center;
}
</style>
