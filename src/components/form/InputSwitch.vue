<template>
    <div :class="bemm()">
        <div :class="bemm('control-container')">
            <span v-for="(v) in formattedValues" @click="handleClick(v.value)"
                :class="[bemm('option'), bemm('option', `${v.label}`), bemm('option', model === v.value ? 'active' : 'inactive')]">
                <Icon :class="bemm('icon')" v-if="v.icon" :name="v.icon" />
               <span :class="bemm('option-label')">{{ v.label }}</span>
            </span>
        </div>
        <label for="test" :class="bemm('label')" v-if="label">
            {{ label }}
        </label>
    </div>
</template>

<script lang="ts" setup>
import { PropType, computed } from "vue";
import { useBemm } from 'bemm';

import Icon from '@/components/Icon.vue';
import { SwitchOption } from "./InputSwitch.model";

const bemm = useBemm('input-switch');

const props = defineProps({
    label: {
        type: String,
        default: ""
    },
    options: {
        type: Array as PropType<string[] | SwitchOption[]>,
        default: ""
    },

})

const model = defineModel()
const emit = defineEmits(["update:modelValue"]);

const formattedValues = computed<SwitchOption[]>(() => {

    if (typeof props.options[0] == 'string') {
        return props.options.map((v) => {
            return {
                label: v,
                value: v,
                icon: null
            } as SwitchOption
        })
    }
    else if (typeof props.options[0] == 'object' && props.options[0].value && props.options[0].label) {
        return props.options as SwitchOption[]
    } else {
        return []
    }


})

const handleClick = (value: string) => {
    emit('update:modelValue', value)
}

</script>


<style lang="scss" src="./Form.scss"></style>