<template>
    <div class="m-2">

        <div class="row d-flex text-center mt-5">
            <h1>Kalkulator średniej ocen</h1>
            <p>Wprowadź przedmioty oraz oceny.</p>
        </div>
        <div class="row">
            <div class="col"></div>
            <div class="col-4">
                <div class="form-floating mb-3">
                    <input v-model="subject_input" type="text" class="form-control" id="subject"
                        placeholder="Np. matematyka">
                    <label for="subject">Nazwa przedmiotu</label>
                </div>
            </div>
            <div class="col-4">
                <div class="form-floating mb-3">
                    <input v-model="grade_input" type="number" class="form-control" id="grade" placeholder="Np. 4.5"
                        min="2.0" max="5.0" step="0.5">
                    <label for="grade">Ocena</label>
                </div>
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
                <button class="btn btn-primary" @click="addSubject()"><i class="bi bi-plus-lg me-1"></i>Dodaj nowy przedmiot</button>
            </div>
            <div class="col"></div>
        </div>
        <div class="row">
            <div class="col"></div>
            <div class="col-5">
                <div class="row g-2 my-2">
                    <table v-if="subject_grade.length > 0" class="table table-striped">
                        <thead>
                            <tr>
                                <th scope="col">Przedmiot</th>
                                <th scope="col" class="text-center">Ocena</th>
                                <th scope="col"></th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr v-for="row in subject_grade" class="align-middle">
                                <td>{{ row.subject }}</td>
                                <td class="text-center">{{ row.grade }}</td>
                                <td class="text-center">
                                    <button class="btn btn-outline-danger" @click="removeSubject(row.subject, row.grade)">
                                        <i class="bi bi-x"></i>
                                    </button>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </div>
            <div class="col-5">
                <div class="container d-flex flex-column justify-content-center align-items-center">
                    <div class="mt-4">
                        <span v-if="avg_grade > 0" class="fs-5 text-center">Twoja średnia ocen: <strong>{{ avg_grade
                        }}</strong></span>
                    </div>
                    <div>
                        <br v-if="grade_sugestion > 1">
                        <span v-if="grade_sugestion == 2" class="text-danger fs-5">Niedostateczny</span>
                        <span v-if="grade_sugestion == 3" class="text-warning fs-5">Dostateczny</span>
                        <span v-if="grade_sugestion == 4" class="text-primary fs-5">Dobry</span>
                        <span v-if="grade_sugestion == 5" class="text-success fs-5">Bardzo Dobry</span>
                    </div>
                </div>
            </div>
            <div class="col"></div>
        </div>
    </div>
</template>

<script lang="ts" setup>
import { ref, watch } from 'vue'

let error = ref('')

let subject_grade = ref<Array<{ subject: String, grade: Number | null }>>([])
let avg_grade = ref<Number>(0)
let grade_sugestion = ref<Number>(0)

let subject_input = ref<String>('')
let grade_input = ref<Number | null>(null)

watch(subject_grade, (newVal) => {
    let sum = 0
    let count = 0
    newVal.forEach((row) => {
        if (row.grade !== null) {
            sum += row.grade
            count++
        }
    })
    avg_grade.value = sum / count

    if (Number.isNaN(avg_grade.value)) {
        avg_grade.value = 0
    }
}, { deep: true })

watch(avg_grade, (newVal) => {
    if (newVal < 2.0) {
        grade_sugestion.value = 1
    }else if (newVal < 2.5) {
        grade_sugestion.value = 2
    } else if (newVal < 3.5) {
        grade_sugestion.value = 3
    } else if (newVal < 4.5) {
        grade_sugestion.value = 4
    } else if (newVal <= 5.0) {
        grade_sugestion.value = 5
    } else {
        grade_sugestion.value = 5
    }
})

function addSubject() {
    if (subject_input.value === '' || grade_input.value === null) {
        error.value = 'Wypełnij wszystkie pola!'
        return
    } else if (grade_input.value < 2.0 || grade_input.value > 5.0) {
        error.value = 'Ocena musi być z przedziału 2.0 - 5.0!'
        return
    } else if (subject_grade.value.some((row) => {
        return row.subject.toLowerCase() === subject_input.value.toLowerCase()
    })) {
        error.value = 'Taki przedmiot już istnieje!'
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

<style></style>