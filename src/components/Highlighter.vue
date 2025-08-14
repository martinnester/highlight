<template>
  <Composer v-for="item in highlightComposition" :composees="item.composees">
    {{ item.subtext }}
  </Composer>
</template>

<script lang="ts">
import { computed, watchEffect, type Component } from "vue";
import Composer, { Composee } from "../components/Composer.vue";
type Range = { start: number; end: number };

class Highlight {
  readonly where: Range;
  readonly composee: InstanceType<typeof Composee>;
  constructor(where: Range, composee: InstanceType<typeof Composee>) {
    this.where = where;
    this.composee = composee;
  }
}

export function highlight<P>(where: Range, is: Component<P>, props: P) {
  return new Highlight(where, new Composee<any>(is, props));
}
</script>

<script setup lang="ts">
const props = withDefaults(
  defineProps<{
    highlights?: Highlight[];
    text: string;
  }>(),
  {
    highlights: () => [],
  }
);

const highlightComposition = computed(() =>
  props.highlights
    .flatMap(({ where: { start, end }, composee }, index) => [
      { start, end: undefined, composee, index },
      { start: undefined, end, composee, index },
    ])
    .sort(
      (a, b) =>
        (a?.start ?? a?.end) - (b?.start ?? b?.end) ||
        +(a.end !== undefined && b.start !== undefined) ||
        +(a.start !== undefined && b.end !== undefined) * -1 ||
        a.index - b.index
    )
    .reduce<
      {
        softComposees: {
          state: "pending" | "active" | "delete";
          composee: Composee;
        }[];
        subtext: string;
        pos: number;
      }[]
    >(
      (
        [{ softComposees, subtext, pos }, ...accumulate],
        { start, end, composee }
      ) => [
        {
          softComposees:
            start !== undefined
              ? [
                  ...softComposees
                    .filter((el) => el.state !== "delete")
                    .map((el) =>
                      el.state === "pending"
                        ? { ...el, state: "active" as const }
                        : el
                    ),
                  { composee, state: "pending" },
                ]
              : softComposees
                  .filter((el) => el.state !== "delete")
                  .map((el) =>
                    el.composee === composee
                      ? { ...el, state: "delete" as const }
                      : el.state === "pending"
                      ? { ...el, state: "active" }
                      : el
                  ),
          subtext: props.text.slice(pos, start ?? end),
          pos: start ?? end,
        },
        { softComposees: softComposees, subtext, pos },
        ...accumulate,
      ],
      [{ softComposees: [], subtext: "", pos: 0 }]
    )
    .flatMap((el, notFirst) =>
      notFirst
        ? [el]
        : [
            {
              pos: props.text.length,
              softComposees: [],
              subtext: props.text.slice(el.pos, props.text.length),
            },
            el,
          ]
    )
    .flatMap(({ subtext, softComposees }) => [
      {
        subtext,
        composees: softComposees
          .filter((el) => el.state !== "pending")
          .map((el) => el.composee),
      },
    ])
    .reverse()
    .slice(1)
);

watchEffect(() => {
  console.log(highlightComposition.value);
});
</script>
