<template>
    <div>
        <button class="btn btn-primary" @click="exportData">Export to CSV</button>
    </div>
</template>

<script lang="ts" setup>
import { ref } from 'vue';

const props = defineProps<{
    subject_grade: Array<{ subject: string; grade: number | null }>
    avg_grade: number
}>()

function exportData() {
    const csvContent = [
        ['Subject', 'Grade'],
        ...props.subject_grade.map(item => [item.subject, item.grade?.toString() ?? '']),
        ['Average Grade', props.avg_grade.toString()]
    ].map(row => row.join(',')).join('\n');

    const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' });
    const link = document.createElement('a');
    if (link.download !== undefined) {
        const url = URL.createObjectURL(blob);
        link.setAttribute('href', url);
        link.setAttribute('download', 'grades.csv');
        link.style.visibility = 'hidden';
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);
    }
}

</script>

<style scoped></style>