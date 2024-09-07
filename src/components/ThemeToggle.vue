<template>
    <div class="d-flex justify-content-end">
        <i :class="theme === 'dark' ? 'bi bi-moon' : 'bi bi-sun'" @click="toggleTheme" role="button"></i>
    </div>
</template>

<script lang="ts" setup>
import { ref } from 'vue';

const theme = ref('light');

function toggleTheme() {
    const htmlEl = document.documentElement;
    const currentTheme = htmlEl.getAttribute('data-bs-theme');
    theme.value = currentTheme === 'dark' ? 'light' : 'dark';
    htmlEl.setAttribute('data-bs-theme', currentTheme === 'dark' ? 'light' : 'dark');
}
</script>