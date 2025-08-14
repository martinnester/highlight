<template>
    <template v-if="composees.length === 0">
            <slot/>
    </template>
    <template v-else>
        <component :is="composees[0].is" v-bind="composees[0].props">
            <Composer :composees="composees.slice(1)">
                <slot/>
            </Composer>
        </component>
    </template>
</template>

<script lang="ts">
import { type Component } from 'vue';

export class Composee<P = any> {
    readonly is: Component<P>;
    readonly props: P;
    constructor(is: Component<P>, props: P) {
        this.is = is;
        this.props = props;
    }
}

</script>

<script lang="ts" setup>

withDefaults(defineProps<{
    composees?: Composee[]
}>(), {
    composees: () => [],
});

</script>