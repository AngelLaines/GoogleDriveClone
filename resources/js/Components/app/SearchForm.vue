<script setup>
import { useForm, router } from '@inertiajs/vue3';
import TextInput from '../TextInput.vue';
import { ref, onMounted } from 'vue';
import {emitter, ON_SEARCH} from "@/event-bus.js";

const search = ref('');
let params = '';

function onSearch() {
    params.set('search', search.value)
    router.get(window.location.pathname + '?' + params.toString())

    emitter.emit(ON_SEARCH, search.value)
}

onMounted(() => {
    params = new URLSearchParams(window.location.search)
    search.value = params.get('search')
})
</script>

<template>
    <div 
        class="w-[600px] h-[80px] flex items-center"
    >
        <TextInput 
            type="text" 
            class="block w-full mr-2" 
            v-model="search" 
            autocomplete 
            @keyup.enter.prevent="onSearch"
            placeholder="Search for files and folders"
        />
    </div>
</template>