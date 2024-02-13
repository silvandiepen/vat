<template>
    <div :class="bemm()">
        <label :class="bemm('label')" v-if="label">
            {{ label }}
        </label>

        <div :class="bemm('control-container')">
            <input style="width: 60px" type="number" :class="bemm('value')" v-model="model" :min="min" :max="max"
                :step="step" v-on:input="emit('update:modelValue', numberModel)" />
            <input :class="bemm('control')" :placeholder="placeholder" type="range" v-model="model" :min="min" :max="max"
                :step="step" v-on:input="emit('update:modelValue', numberModel)" />
        </div>
    </div>
</template>

<script lang="ts" setup>
import { computed } from "vue";
import { useBemm } from 'bemm';
const bemm = useBemm('input-range');

const model = defineModel<number | string>()
const emit = defineEmits(["update:modelValue"]);

defineProps({
    label: {
        type: String,
        default: ""
    },
    placeholder: {
        type: String,
        default: ""
    },
    min: {
        type: Number,
        default: 0
    },
    max: {
        type: Number,
        default: 100
    },
    step: {
        type: Number,
        default: 1
    }
})
const numberModel = computed(() => {
    if (typeof model.value == "string") return parseInt(model.value);
    return model.value;
})
</script>
<style lang="scss" src="./Form.scss"></style>