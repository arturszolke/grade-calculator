<template>
    <table v-if="grades.length > 0" class="table table-striped">
        <thead>
            <tr>
                <th scope="col" @click="sortBy('subject')">Subject <i :class="sortIcon('subject')"></i></th>
                <th scope="col" class="text-center" @click="sortBy('grade')">Grade <i :class="sortIcon('grade')"></i>
                </th>
                <th scope="col"></th>
            </tr>
        </thead>
        <tbody>
            <tr v-for="row in sortedGrades" class="align-middle" :class="row.grade == 2 ? 'table-danger' : ''">
                <td>{{ row.subject }}</td>
                <td class="text-center">{{ row.grade }}</td>
                <td class="text-center">
                    <button class="btn btn-outline-danger" @click="$emit('remove', row.subject, row.grade)">
                        <i class="bi bi-x"></i>
                    </button>
                </td>
            </tr>
        </tbody>
    </table>
</template>

<script lang="ts" setup>
import { ref, computed } from 'vue';

const props = defineProps<{
    grades: Array<{ subject: string, grade: number | null }>
}>();

defineEmits<{
    (e: 'remove', subject: string, grade: number | null): void
}>();

const sortKey = ref<'subject' | 'grade' | null>(null);
const sortOrder = ref<'asc' | 'desc' | null>(null);

const sortedGrades = computed(() => {
    if (sortKey.value === null) return props.grades;
    return [...props.grades].sort((a, b) => {
        if (sortKey.value === null) return 0;
        let modifier = sortOrder.value === 'asc' ? 1 : -1;
        if (sortKey.value !== null) {
            const aValue = a[sortKey.value];
            const bValue = b[sortKey.value];
            if (aValue !== null && bValue !== null) {
                if (aValue < bValue) return -1 * modifier;
                if (aValue > bValue) return 1 * modifier;
            }
        }
        return 0;
    });
});

function sortBy(key: 'subject' | 'grade') {
    if (key === sortKey.value) {
        sortOrder.value = sortOrder.value === 'asc' ? 'desc' : sortOrder.value === 'desc' ? null : 'asc';
        if (sortOrder.value === null) {
            sortKey.value = null;
        }
    } else {
        sortKey.value = key;
        sortOrder.value = 'asc';
    }
}

function sortIcon(key: 'subject' | 'grade') {
    if (sortKey.value === key) {
        if (sortOrder.value === 'asc') return 'bi bi-sort-up';
        if (sortOrder.value === 'desc') return 'bi bi-sort-down';
    }
    return 'bi bi-filter';
}
</script>