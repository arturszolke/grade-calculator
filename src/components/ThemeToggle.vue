<template>
    <div class="d-flex justify-content-end">
        <i :class="theme === 'dark' ? 'bi bi-moon' : 'bi bi-sun'" @click="toggleTheme" role="button"></i>
    </div>
</template>

<script lang="ts" setup>
import { ref, onMounted } from 'vue';

const theme = ref('light');

onMounted(() => {
    const savedTheme = localStorage.getItem('theme') || 'light';
    theme.value = savedTheme;
    document.documentElement.setAttribute('data-bs-theme', savedTheme);
});

function toggleTheme() {
    theme.value = theme.value === 'dark' ? 'light' : 'dark';
    document.documentElement.setAttribute('data-bs-theme', theme.value);
    localStorage.setItem('theme', theme.value);
}
</script>