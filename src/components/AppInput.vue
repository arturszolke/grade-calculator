<template>
    <div class="form-floating mb-3">
        <input v-model="model" :type="type" :id="id" :placeholder="placeholder" class="form-control"
            @input="validateInput()" @focus="$emit('focus')">
        <label :for="id">{{ placeholder }}</label>
        <span v-if="errorMessage.length > 0" class="text-danger">{{ errorMessage }}</span>
    </div>
</template>

<script setup lang="ts">
import { defineModel, ref } from 'vue'

const props = defineProps({
    type: {
        type: String,
        default: 'text'
    },
    id: {
        type: String
    },
    placeholder: {
        type: String,
        required: true
    },
    validate: {
        type: Function,
        default: () => () => true
    }
})

const model = defineModel();
const errorMessage = ref('');

const emit = defineEmits(['focus']);

const validateInput = () => {
    errorMessage.value = props.validate(model.value) ? '' : 'Invalid input';
}
</script>

<style lang="css" scoped></style>