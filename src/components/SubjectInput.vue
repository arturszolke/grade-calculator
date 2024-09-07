<template>
    <div class="position-relative">
        <AppInput :model-value="modelValue" @update:model-value="$emit('update:modelValue', $event)" type="text"
            placeholder="E.g. Mathematics" label="Subject Name" @focus="showSuggestions = true"
            @input="onSubjectInput" />
        <ul v-if="showSuggestions && filteredSubjects.length > 0"
            class="list-group position-absolute w-100 suggestion-list" ref="suggestionList">
            <li v-for="subject in filteredSubjects" :key="subject" class="list-group-item list-group-item-action"
                @click="selectSubject(subject)">
                {{ subject }}
            </li>
        </ul>
    </div>
</template>

<script lang="ts" setup>
import { ref, computed } from 'vue';
import AppInput from '@/components/AppInput.vue';

const props = defineProps<{
    commonSubjects: string[];
    modelValue: string;
}>();

const emit = defineEmits<{
    (e: 'update:modelValue', value: string): void;
}>();

const showSuggestions = ref(false);

const filteredSubjects = computed(() => {
    if (!props.modelValue) return [];
    const input = props.modelValue.toLowerCase();
    return props.commonSubjects.filter(subject =>
        subject.toLowerCase().startsWith(input)
    ).slice(0, 5);
});

function selectSubject(subject: string) {
    emit('update:modelValue', subject);
    showSuggestions.value = false;
}

function onSubjectInput(event: Event) {
    showSuggestions.value = true;
    emit('update:modelValue', (event.target as HTMLInputElement).value);
}
</script>

<style scoped>
.suggestion-list {
    max-height: 200px;
    overflow-y: auto;
    z-index: 1001;
    top: 100%;
    margin-top: -1px;
    border-top-left-radius: 0;
    border-top-right-radius: 0;
}
</style>