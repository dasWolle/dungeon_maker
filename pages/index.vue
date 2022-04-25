<template>
  <div class="container">
    <h1 class="text-3xl" @click="randomize">Dungeon Maker</h1>
    <div class="flex">
      <div ref="floor" class="floor grid grid-cols-3 mx-auto">
        <template v-for="(row, rowIndex) in tiles">
          <template v-for="(tile, column) in row" :key="tile.id">
            <Tile
                :active="(activeTile.column === column) && (activeTile.row === rowIndex)"
                :form="tile.form"
                :orientation="tile.orientation"
                @click="activeTile= { column, row: rowIndex }"
            />
          </template>
        </template>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "@vue/runtime-core";

export default defineComponent({
  name: "index",
  data() {
    return {
      activeTile: { row: -1, column: -1 },
      columns: 3,
      rows: 3,
      tiles: [
        [ { form: 1, id: 0, orientation: 1 }, { form: 2, id: 1, orientation: 2 }, { form: 1, id: 2, orientation: 2 } ],
        [ { form: 0, id: 3, orientation: 1 }, { form: 0, id: 4, orientation: 1 }, { form: 0, id: 5, orientation: 1 } ],
        [ { form: 1, id: 6, orientation: 0 }, { form: 2, id: 7, orientation: 0 }, { form: 1, id: 8, orientation: 3 } ]
      ],
    };
  },
  methods: {
    keypress(event: KeyboardEvent): void {
      console.log(event.key);
      if (event.key === "Tab") {
        event.preventDefault();
        event.stopPropagation();
        if (this.activeTile.row === -1) this.activeTile = { column: 0, row: 0 };
        else {
          if (this.activeTile.row === (this.rows - 1) && this.activeTile.column === (this.columns - 1)) this.activeTile = { column: 0, row: 0 };
          else {
            if (this.activeTile.column === (this.columns - 1)) this.activeTile = { column: 0, row: this.activeTile.row + 1 };
            else this.activeTile = { column: this.activeTile.column + 1, row: this.activeTile.row };
          }
        }
      } else if (event.key === "ArrowUp" || event.key === "w") {
        const up = this.tiles[this.activeTile.row - 1][this.activeTile.column];
        const active = this.tiles[this.activeTile.row][this.activeTile.column];
        this.tiles[this.activeTile.row - 1][this.activeTile.column] = active;
        this.tiles[this.activeTile.row][this.activeTile.column] = up;
      }
    },
    random(): number { return Math.floor(Math.random() * 4); },
    randomize(): void {
      this.tiles.forEach(row => {
        row.forEach(tile => {
          tile.form = this.random();
          tile.orientation = this.random();
        });
      });
    },
  },
  mounted() {
    if (process.client) window.addEventListener("keydown", this.keypress);
  }
});
</script>

<style lang="scss">
  .floor {
    @apply bg-earth;
    gap: 2px;
    --tile-size: 4rem;
  }
</style>