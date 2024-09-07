<template>
    <div class="m-2 overflow-hidden">
        <div class="d-flex justify-content-end">
            <i v-if="theme === 'dark'" class="bi bi-moon" @click="toggleTheme" role="button"></i>
            <i v-else class="bi bi-sun" @click="toggleTheme" role="button"></i>
        </div>
        <div class="row d-flex text-center mt-5 app-title">
            <h1>Grade Average Calculator</h1>
            <p>Enter subjects and grades.</p>
        </div>
        <div class="row">
            <div class="col"></div>
            <div class="col-4">
                <AppInput v-model="subject_input" type="text" placeholder="E.g. Mathematics" label="Subject Name" />
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
                <div class="container d-flex flex-column justify-content-center align-items-center">
                    <div class="mt-4">
                        <span v-if="Number(avg_grade) > 0" class="fs-5 text-center">Your average grade: <strong>{{
                            avg_grade.toFixed(3)
                                }}</strong></span>
                    </div>
                    <div>
                        <br v-if="Number(grade_sugestion) > 1">
                        <span v-if="grade_sugestion == 2" class="text-danger fs-5">Unsatisfactory</span>
                        <span v-if="grade_sugestion == 3" class="text-warning fs-5">Satisfactory</span>
                        <span v-if="grade_sugestion == 4" class="text-primary fs-5">Good</span>
                        <span v-if="grade_sugestion == 5" class="text-success fs-5">Very Good</span>
                    </div>
                </div>
            </div>
            <div class="col"></div>
        </div>
    </div>
</template>

<script lang="ts" setup>
import { onMounted, ref, watch, computed } from 'vue'
import AppInput from '@/components/AppInput.vue'
import GradeTable from '@/components/GradeTable.vue'

const error = ref('')

const subject_grade = ref<Array<{ subject: String, grade: Number | null }>>([])
const avg_grade = ref<Number>(0)
const grade_sugestion = ref<Number>(0)

const subject_input = ref<String>('')
const grade_input = ref<Number | null>(null)

const theme = ref('light')

onMounted(() => {
    const htmlEl = document.documentElement;
    const currentTheme = htmlEl.getAttribute('data-bs-theme');
    theme.value = currentTheme === 'dark' ? 'dark' : 'light';
})

watch(subject_grade, (newVal) => {
    const grades = newVal.map(row => row.grade).filter(Boolean)
    const sum = grades.reduce((a, b) => Number(a) + Number(b), 0)
    const count = grades.length
    avg_grade.value = Number(sum) / count || 0
}, { deep: true });

watch(avg_grade, (newVal) => {
    if (Number(newVal) < 2.0) grade_sugestion.value = 1
    else if (Number(newVal) < 2.5) grade_sugestion.value = 2
    else if (Number(newVal) < 3.5) grade_sugestion.value = 3
    else if (Number(newVal) < 4.5) grade_sugestion.value = 4
    else grade_sugestion.value = 5
})

function addSubject() {
    if (subject_input.value.trim() === '' || grade_input.value === null) {
        error.value = 'Fill in all fields!'
        return
    } else if (Number(grade_input.value) < 2.0 || Number(grade_input.value) > 5.0) {
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

function toggleTheme() {
    const htmlEl = document.documentElement;
    const currentTheme = htmlEl.getAttribute('data-bs-theme');
    theme.value = currentTheme === 'dark' ? 'light' : 'dark';
    htmlEl.setAttribute('data-bs-theme', currentTheme === 'dark' ? 'light' : 'dark');
}
</script>