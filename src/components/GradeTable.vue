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
    if (!sortKey.value) return props.grades;
    const modifier = sortOrder.value === 'asc' ? 1 : -1;
    return [...props.grades].sort((a, b) => {
        const key = sortKey.value!;
        const aValue = a[key];
        const bValue = b[key];
        if (aValue === bValue) return 0;
        return (aValue! < bValue! ? -1 : 1) * modifier;
    });
});

function sortBy(key: 'subject' | 'grade') {
    if (key === sortKey.value) {
        sortOrder.value = sortOrder.value === 'asc' ? 'desc' : null;
        if (!sortOrder.value) sortKey.value = null;
    } else {
        sortKey.value = key;
        sortOrder.value = 'asc';
    }
}

function sortIcon(key: 'subject' | 'grade') {
    if (sortKey.value === key) {
        return sortOrder.value === 'asc' ? 'bi bi-sort-up' : 'bi bi-sort-down';
    }
    return 'bi bi-filter';
}
</script>