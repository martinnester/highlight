<script setup lang="ts">
import Highlighter, { highlight } from "./components/Highlighter.vue";
import Tag from "./components/Tag.vue";
import WithIcon from "./components/WithIcon.vue";

const loremIpsum = 'Lorem ipsum dolor sit amet, consectetur adipiscing elit. Donec sit amet massa a nisi placerat varius. Integer dictum nisl eu sapien semper, ut venenatis risus luctus. Quisque at elementum quam. Sed in ipsum nibh. Proin et bibendum urna. Etiam id mauris nunc. Aenean mollis, felis eu malesuada auctor, purus velit elementum elit, ut laoreet orci purus nec magna.';

</script>

<template>
  <Highlighter
    :text="loremIpsum"
    :highlights="[
      highlight({ start: 1, end: 35 }, Tag, { tag: 'mark' }),
      highlight({ start: 5, end: 80 }, Tag, { tag: 'strong' }),
      highlight({ start: 15, end: 30 }, Tag, { tag: 'em' }),
      highlight({ start: 70, end: 90 }, Tag, { tag: 'a' }),
      highlight({ start: 105, end: 115 }, Tag, { tag: 'mark' }),
      highlight({ start: 107, end: 112 }, Tag, { tag: 'strong' }),
      highlight({ start: 102, end: 109 }, WithIcon, {}),


    ]"
  />
</template>

<style scoped></style>
