<template>
  <div class="home">
    <v-row no-gutters>
      <v-col cols="auto"> Difficulty </v-col>
      <v-col cols="auto">
        <v-select v-model="difficulty" :items="difficultyList"></v-select>
      </v-col>
    </v-row>
    <v-container
      align-content="center"
      justify="center"
      style="width: 454px; padding: 0; border: 2px solid black;"
    >
      <v-row no-gutters v-for="(row, index) in puzzle" :key="index">
        <v-col
          v-for="(col, i) in row"
          :key="i"
          cols="auto"
          :style="
            (index + 1) % 3 == 0
              ? 'border-bottom: 2px solid black;'
              : 'border-bottom: 1px solid grey;' + 'cursor: default !important;'
          "
        >
          <v-card
            elevation="0"
            height="50"
            width="50"
            tile
            :style="
              'border: 1px solid grey;' +
              ((i + 1) % 3 == 0
                ? 'border-right: 2px solid black;'
                : 'border-right: 1px solid grey;')
                +'  cursor: default !important;'
                
            "
          >
            <v-row
              class="fill-height pa-0 ma-0"
              align="center"
              justify="center"
            >
              <v-text-field
                class="centered-input pa-0 ma-0"
                hight="100%"
                type="number"
                min="1"
                max="9"
                solo
                v-model="col.value"
                :readonly="col.isReadOnly"
                @focus="col.isSelected = true"
                @blur="col.isSelected = false"
                hide-spin-buttons
                hide-details
                :maxlength="1"
                :backgroundColor="col.isSelected ? '#BBDEFB' : '#FFFFFF'"
                @keydown="handleKeyDown"
                style="cursor: default !important; border-radius: 0;"
              ></v-text-field>
            </v-row>
          </v-card>
        </v-col>
      </v-row>
    </v-container>
    {{ selectedIndex }}
    {{ puzzle }}
  </div>
</template>

<script>
import { getSudoku } from "sudoku-gen";
export default {
  name: "HomeView",
  data() {
    return {
      difficulty: "easy",
      difficultyList: [
        { text: "Easy", value: "easy" },
        { text: "Medium", value: "medium" },
        { text: "Hard", value: "hard" },
        { text: "Expert", value: "expert" },
      ],
      puzzle: [],
      solution: [],
      selectedIndex: { x: null, y: null },
    };
  },
  computed: {
    formattedPuzzle() {
      const puzzle = this.puzzle;
      console.log(puzzle);
      return puzzle.map((a) =>
        a.map((b) => ({
          isSelected: 0,
          value: b != "-" ? b : "",
          isReadOnly: b != "-",
        }))
      );
    },
  },
  watch: {
    difficulty() {
      this.createQuestion();
    },
  },
  mounted() {
    this.createQuestion();
  },
  methods: {
    handleKeyDown(event) {
      const key = event.key;
      const keyCode = event.keyCode;
      const ignoreKeys = [8, 9, 37, 38, 39, 40, 46]
      if (ignoreKeys.includes(keyCode)) return;

      if (!/^[1-9]$/.test(key)) {
        event.preventDefault();
        return;
      }
      event.target.value = "";
    },
    setSelected(i, j) {
      let x = this.selectedIndex.x,
        y = this.selectedIndex.y;
      if ((x != null || y != null) && x != i && y != j) {
        this.puzzle[x][y] = Object.assign(this.puzzle[x][y], {
          isSelected: !this.puzzle[x][y].isSelected,
        });
      }
      this.selectedIndex = { x: i, y: j };
      this.puzzle[i][j] = Object.assign(this.puzzle[i][j], {
        isSelected: !this.puzzle[i][j].isSelected,
      });
    },
    createQuestion() {
      this.puzzle = [];
      const sudoku = getSudoku(this.difficulty);
      let puzzle = sudoku.puzzle.split("");
      while (puzzle.length > 0) {
        this.puzzle.push(
          puzzle.splice(0, 9).map((b) => ({
            isSelected: false,
            value: b != "-" ? b : "",
            isReadOnly: b != "-",
          }))
        );
      }
      this.solution = sudoku.solution.split("");
    },
  },
};
</script>


<style scoped>
.centered-input >>> input {
  caret-color: transparent !important;
  cursor: default !important;
  height: 100%;
  width: 100%;
  text-align: center;
}
.centered-input >>> .v-input__slot 
  {
    padding: 0px;
    margin: 0px;
    width: 100%;
    cursor: default;
}

</style>