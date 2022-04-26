<template>
  <div class="flex flex-col gap-4">
    <h1 class="text-3xl mx-auto" @click="randomize">Dungeon Maker</h1>
    <div class="flex gap-8 justify-center">
      <label>Zeilen: <input v-model="rowsC" class="input" max="30" min="1" type="number" @focusin="activeTile = { row: -1, column: -1 }"></label>
      <label>Spalten: <input v-model="columnsC" class="input" max="10" min="1" type="number" @focusin="activeTile = { row: -1, column: -1 }"></label>
    </div>
    <div class="flex">
      <div ref="floor" :style="`--columns:${columns};`" class="floor grid mx-auto">
        <template v-for="(row, rowIndex) in tiles">
          <template v-for="(tile, column) in row" :key="tile.id">
            <Tile
                :active="(activeTile.column === column) && (activeTile.row === rowIndex)"
                :data-row="rowIndex"
                :form="tile.form"
                :rotation="tile.rotation"
                @click="activeTile= { column, row: rowIndex }"
                @focusin="activeTile={row: rowIndex, column: column}"
            />
          </template>
        </template>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "@vue/runtime-core";
import { Tile } from "~/env";

export default defineComponent({
  name: "index",
  data() {
    return {
      activeTile: { row: -1, column: -1 },
      columns: 3,
      lastId: -1,
      rows: 4,
      tiles: [] as Tile[][],
    };
  },
  computed: {
    columnsC: {
      set(val) {
        if (val > 0) this.columns = val;
        else this.columns = 1;
      },
      get() { return this.columns; }
    },
    rowsC: {
      set(val) {
        if (val > 0) this.rows = val;
        else this.rows = 1;
      },
      get() { return this.rows; }
    }
  },
  methods: {
    addTile(row: Tile[]): void {
      row.push({ form: this.random(), id: this.lastId + 1, rotation: this.random() });
      this.lastId++;
    },
    addRow() {
      let row = [];
      for (let k = 0; k < this.columns; k++) this.addTile(row);
      this.tiles.push(row);
    },
    changeTile(key: string, row: number, col: number): void {
      if (key === "w") {
        const up = this.tiles[row - 1][col];
        this.tiles[row - 1][col] = this.tiles[row][col];
        this.tiles[row][col] = up;
      } else if (key === "s") {
        const down = this.tiles[row + 1][col];
        this.tiles[row + 1][col] = this.tiles[row][col];
        this.tiles[row][col] = down;
      } else if (key === "a") {
        const left = this.tiles[row][col - 1];
        this.tiles[row][col - 1] = this.tiles[row][col];
        this.tiles[row][col] = left;
      } else if (key === "d") {
        const up = this.tiles[row][col + 1];
        this.tiles[row][col + 1] = this.tiles[row][col];
        this.tiles[row][col] = up;
      } else if (key === "q") this.tiles[row][col].rotation--;
      else if (key === "e") this.tiles[row][col].rotation++;
    },
    keypress(event: KeyboardEvent): void {
      const row = this.activeTile.row;
      const col = this.activeTile.column;
      if ([ "q", "w", "e", "a", "s", "d" ].includes(event.key)) this.changeTile(event.key, row, col);
      else if ([ "Tab", "ArrowUp", "ArrowDown", "ArrowLeft", "ArrowRight" ].includes(event.key)) this.moveSelection(event.key, row, col);
    },
    moveSelection(key: string, row: number, col: number): void {
      if (key === "ArrowUp") {
        if (row === 0) this.activeTile.row = this.rows - 1;
        else this.activeTile.row--;
      } else if (key === "ArrowDown") {
        if (row === (this.rows - 1)) this.activeTile.row = 0;
        else this.activeTile.row++;
      } else if (key === "ArrowLeft") {
        if (col === 0) this.activeTile.column = this.columns - 1;
        else this.activeTile.column--;
      } else if (key === "ArrowRight") {
        if (col === (this.columns - 1)) this.activeTile.column = 0;
        else this.activeTile.column++;
      }
    },
    random(): number { return Math.floor(Math.random() * 4); },
    randomize(): void {
      for (let i = 0; i < this.rows; i++) {
        let row = [];
        for (let k = 0; k < this.columns; k++) {
          row.push({ form: this.random(), id: (i * this.rows) + k, rotation: this.random() });
        }
        this.tiles.push(row);
      }
    },
  },
  watch: {
    columns(val: number, old: number): void {
      if (val < old) {
        while (val < this.tiles[0].length) {
          this.tiles.forEach((row: Tile[]) => row.pop());
        }
      } else if (val > old) this.tiles.forEach((row: Tile[]) => this.addTile(row));
    },
    rows(val: number, old: number): void {
      if (val < old) {
        while (val < this.tiles.length) this.tiles.pop();
      } else if (val > old) this.addRow();
    },
  },
  mounted() {
    if (process.client) window.addEventListener("keydown", this.keypress);
    this.randomize();
  }
});
</script>

<style lang="scss">
  .floor {
    @apply bg-earth;
    gap: 2px;
    grid-template-columns: repeat(var(--columns), 1fr);
    --tile-size: 4rem;
  }
  .input {
    @apply w-16 border border-gray-500 rounded-lg px-2 pb-1;
  }
</style>