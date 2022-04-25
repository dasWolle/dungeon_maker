<template>
  <div :class="tileClass" class="tile_wrapper">
    <TileLine v-if="formC === 0" class="tile"/>
    <TileCorner v-if="formC === 1" class="tile"/>
    <TileT v-if="formC === 2" class="tile"/>
    <TileCross v-if="formC === 3" class="tile"/>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "@vue/runtime-core";

export default defineComponent({
  name: "Tile",
  props: {
    active: { type: Boolean, default: false },
    form: { type: [ Number, String ] },
    orientation: { type: [ Number, String ] },
  },
  computed: {
    formC() {
      if (this.form) {
        if (typeof this.form === "string") return parseInt(this.form);
        else return this.form;
      } else return 0;
    },
    orientationC() {
      if (this.orientation) {
        if (typeof this.orientation === "string") return parseInt(this.orientation);
        else return this.orientation;
      } else return 0;
    },
    tileClass() { return `orientation-${this.orientationC}` + (this.active ? " active" : ""); }
  },
});
</script>

<style lang="scss">
  .tile_wrapper {
    transition: transform .2s ease-in-out;
    &.orientation-1 { transform: rotate(90deg); }
    &.orientation-2 { transform: rotate(180deg); }
    &.orientation-3 { transform: rotate(-90deg); }
    &.active { @apply outline outline-2 outline-blue-600; }
  }
  .tile {
    @apply grid;
    grid-template-columns: repeat(3, var(--tile-size));
    grid-template-rows: repeat(3, var(--tile-size));
    * {
      min-height: 4rem;
      min-width: 4rem;
    }
  }
</style>