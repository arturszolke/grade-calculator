<template>
    <div class="m-2 overflow-hidden vh-100">
        <ThemeToggle />
        <div class="row d-flex text-center mt-5 app-title">
            <h1>Grade Average Calculator</h1>
            <p>Enter subjects and grades.</p>
        </div>
        <div class="row">
            <div class="col"></div>
            <div class="col-4">
                <SubjectInput :common-subjects="commonSubjects" v-model="subject_input" />
            </div>
            <div class="col-4">
                <AppInput v-model="grade_input" type="number" placeholder="E.g. 4.5" label="Grade" :min="2.0" :max="5.0"
                    :step="0.5" />
            </div>
            <div class="col"></div>
        </div>
        <div class="row mb-2 text-center">
            <div class="col"></div>
            <div class="col-8">
                <span class="text-danger">{{ error }}</span>
            </div>
            <div class="col"></div>
        </div>
        <div class="row text-center">
            <div class="col"></div>
            <div class="col-8">
                <button class="btn btn-primary" @click="addSubject()"><i class="bi bi-plus-lg me-1"></i>Add new
                    subject</button>
            </div>
            <div class="col"></div>
        </div>
        <div class="row">
            <div class="col"></div>
            <div class="col-5">
                <div class="row g-2 my-2">
                    <GradeTable :grades="subject_grade" @remove="removeSubject" />
                </div>
            </div>
            <div class="col-5">
                <GradeSuggestion :avg-grade="Number(avg_grade)" />
            </div>
            <div class="col"></div>
        </div>
    </div>
</template>

<script lang="ts" setup>
import { ref, watch } from 'vue';
import AppInput from '@/components/AppInput.vue';
import GradeTable from '@/components/GradeTable.vue';
import ThemeToggle from '@/components/ThemeToggle.vue';
import SubjectInput from '@/components/SubjectInput.vue';
import GradeSuggestion from '@/components/GradeSuggestion.vue';

const commonSubjects = [
    'Mathematics', 'Physics', 'Chemistry', 'Biology', 'History',
    'Geography', 'Literature', 'Computer Science', 'Economics',
    'Psychology', 'Sociology', 'Philosophy', 'Art', 'Music'
];

const error = ref('')

const subject_grade = ref<Array<{ subject: string; grade: number | null }>>([])
const avg_grade = ref<Number>(0)

const subject_input = ref<string>('')
const grade_input = ref<number | null>(null)

watch(subject_grade, (newVal) => {
    const grades = newVal.map(row => row.grade).filter(Boolean)
    const sum = grades.reduce((a, b) => Number(a) + Number(b), 0)
    const count = grades.length
    avg_grade.value = Number(sum) / count || 0
}, { deep: true });

function addSubject() {
    if (subject_input.value.trim() === '' || grade_input.value === null) {
        error.value = 'Fill in all fields!'
        return
    } else if (grade_input.value < 2.0 || grade_input.value > 5.0) {
        error.value = 'Grade must be between 2.0 - 5.0!'
        return
    } else if (subject_grade.value.some((row) => {
        return row.subject.toLowerCase() === subject_input.value.toLowerCase()
    })) {
        error.value = 'This subject already exists!'
        return
    }

    subject_grade.value.push({
        subject: subject_input.value,
        grade: grade_input.value
    })
    subject_input.value = ''
    grade_input.value = null
    error.value = ''
}

function removeSubject(subject: String, grade: Number | null) {
    subject_grade.value = subject_grade.value.filter((row) => {
        return row.subject !== subject || row.grade !== grade
    })
}
</script>

<style scoped>
.position-relative {
    z-index: 1000;
}

.list-group {
    max-height: 200px;
    overflow-y: auto;
    z-index: 1001;
}

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