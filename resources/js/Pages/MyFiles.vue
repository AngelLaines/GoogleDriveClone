<script setup>
import AuthenticatedLayout from '@/Layouts/AuthenticatedLayout.vue';


</script>

<template>
    <div>
        <AuthenticatedLayout>
            {{ $page.url }}
        </AuthenticatedLayout>
    </div>
</template>
