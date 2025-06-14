<template>
    <div>
        <div class="mb-2 d-flex align-items-center justify-content-center">
            <select v-model="selectedFileType" class="form-select d-inline-block w-auto me-2">
                <option value="xlsx">Excel</option>
                <option value="csv">CSV</option>
            </select>
            <button class="btn btn-primary" @click="exportData">Export</button>
        </div>
    </div>
</template>

<script lang="ts" setup>
import { ref } from 'vue';
import * as XLSX from 'xlsx';

const props = defineProps<{
    subject_grade: Array<{ subject: string; grade: number | null }>
    avg_grade: number
}>()

const selectedFileType = ref<'csv' | 'xlsx'>('xlsx');

function exportData() {
    const data = [
        ['Subject', 'Grade'],
        ...props.subject_grade.map(item => [item.subject, item.grade?.toString() ?? '']),
        ['Average Grade', props.avg_grade.toString()]
    ];

    if (selectedFileType.value === 'csv') {
        const csvContent = data.map(row => row.join(',')).join('\n');
        const blob = new Blob([csvContent], { type: 'text/csv;charset=utf-8;' });
        downloadFile(blob, 'grades.csv');
    } else {
        const worksheet = XLSX.utils.aoa_to_sheet(data);

        const colWidths = data.reduce((widths, row) => {
            row.forEach((cell, index) => {
                const cellLength = cell ? cell.toString().length : 0;
                widths[index] = Math.max(widths[index] || 0, cellLength);
            });
            return widths;
        }, [] as number[]);

        worksheet['!cols'] = colWidths.map(width => ({ wch: width + 2 }));

        const workbook = XLSX.utils.book_new();
        XLSX.utils.book_append_sheet(workbook, worksheet, 'Grades');
        const excelBuffer = XLSX.write(workbook, { bookType: 'xlsx', type: 'array' });
        const blob = new Blob([excelBuffer], { type: 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet' });
        downloadFile(blob, 'grades.xlsx');
    }
}

function downloadFile(blob: Blob, filename: string) {
    const link = document.createElement('a');
    if (link.download !== undefined) {
        const url = URL.createObjectURL(blob);
        link.setAttribute('href', url);
        link.setAttribute('download', filename);
        link.style.visibility = 'hidden';
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);
    }
}
</script>

<style scoped></style>