<template>
    <div class="container d-flex flex-column justify-content-center align-items-center">
        <div class="mt-4">
            <span v-if="avgGrade > 0" class="fs-5 text-center">Your average grade: <strong>{{ avgGrade.toFixed(3)
                    }}</strong></span>
        </div>
        <div>
            <br v-if="gradeSuggestion > 1">
            <span :class="suggestionClass">{{ suggestionText }}</span>
        </div>
    </div>
</template>

<script lang="ts" setup>
import { computed } from 'vue';

const props = defineProps<{
    avgGrade: number;
}>();

const gradeSuggestion = computed(() => {
    if (props.avgGrade < 2.0) return 1;
    if (props.avgGrade < 2.5) return 2;
    if (props.avgGrade < 3.5) return 3;
    if (props.avgGrade < 4.5) return 4;
    return 5;
});

const suggestionClass = computed(() => {
    switch (gradeSuggestion.value) {
        case 2: return 'text-danger fs-5';
        case 3: return 'text-warning fs-5';
        case 4: return 'text-primary fs-5';
        case 5: return 'text-success fs-5';
        default: return '';
    }
});

const suggestionText = computed(() => {
    switch (gradeSuggestion.value) {
        case 2: return 'Unsatisfactory';
        case 3: return 'Satisfactory';
        case 4: return 'Good';
        case 5: return 'Very Good';
        default: return '';
    }
});
</script>