<script setup>
import { ref } from 'vue';

import AppMenuItem from './AppMenuItem.vue';

const model = ref([
    {
        label: 'Home',
        items: [{ label: 'Dashboard', icon: 'pi pi-fw pi-home', to: '/' }]
    },
    {
        label: 'Prácticas',
        items: [
            { label: 'Formulario RFC', icon: 'pi pi-fw pi-id-card', to: '/uikit/formlayout' },
            { label: 'POS', icon: 'pi pi-fw pi-cart-plus', to: '/uikit/input' },
            { label: 'API', icon: 'pi pi-fw pi-users', to: '/uikit/api' },
            { label: 'UI Charts', icon: 'pi pi-fw pi-chart-line', to: '/uikit/chart' }

        ]

    },
    
]);
</script>

<template>
    <ul class="layout-menu">
        <template v-for="(item, i) in model" :key="item">
            <app-menu-item v-if="!item.separator" :item="item" :index="i"></app-menu-item>
            <li v-if="item.separator" class="menu-separator"></li>
        </template>
        
    </ul>
</template>

<style lang="scss" scoped></style>
