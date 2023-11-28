<script setup>
import { ref } from 'vue';
import ConfirmationDialog from './ConfirmationDialog.vue';
import { useForm } from '@inertiajs/vue3';
import { usePage } from '@inertiajs/vue3';
import { showErrorDialog, showSuccessNotification } from '@/event-bus';
import ShareFilesModal from './ShareFilesModal.vue';


const showConfirmationDialog = ref(false);

const page = usePage();
const form = useForm({
    all:null,
    ids:[],
    parent_id:null
});

const props = defineProps({
    allSelected:{
        type:Boolean,
        required:false,
        default:false
    },
    selectedIds:{
        type:Array,
        required:false
    }
});

const emit = defineEmits(['share'])

function onClick(){
    if (!props.allSelected && !props.selectedIds.length) {
        showErrorDialog('Please select at least one file to share');
        return;
    }
    showConfirmationDialog.value=true;
}

function onCancel(){
    showConfirmationDialog.value=false;
}
function onConfirm(){
    if (props.allSelected) {
        form.all = true;
    }
    else {
        form.ids = props.selectedIds;
    }
    form.post(route('file.share'),{
        onSuccess:()=>{
            showConfirmationDialog.value = false;
            emit('share');
            showSuccessNotification('Selected files have been shared');
        }
    });
}

</script>

<template>
    <button
    @click="onClick"
    class="inline-flex items-center mr-2 px-4 py-2 text-xs font-medium text-gray-900 bg-white border border-gray-200 rounded-lg hover:bg-gray-100 hover:text-blue-700 focus:z-10 focus:ring-2 focus:ring-blue-700 focus:text-blue-700 dark:bg-gray-700 dark:border-gray-600 dark:text-white dark:hover:text-white dark:hover:bg-gray-600 dark:focus:ring-blue-500 dark:focus:text-white"
    >
    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 24 24" fill="currentColor" class="w-4 h-4 mr-2">
        <path fill-rule="evenodd"
              d="M15.75 4.5a3 3 0 11.825 2.066l-8.421 4.679a3.002 3.002 0 010 1.51l8.421 4.679a3 3 0 11-.729 1.31l-8.421-4.678a3 3 0 110-4.132l8.421-4.679a3 3 0 01-.096-.755z"
              clip-rule="evenodd"/>
    </svg>
    Share
    </button>

    <ShareFilesModal v-model="showConfirmationDialog" :all-selected="allSelected" :selected-ids="selectedIds"></ShareFilesModal>
</template>