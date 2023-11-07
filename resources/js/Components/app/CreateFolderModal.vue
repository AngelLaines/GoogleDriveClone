<script setup>
import { useForm, usePage } from '@inertiajs/vue3';
import InputError from '../InputError.vue';
import InputLabel from '../InputLabel.vue';
import Modal from '../Modal.vue';
import TextInput from '../TextInput.vue';
import SecondaryButton from '../SecondaryButton.vue';
import PrimaryButton from '../PrimaryButton.vue';
import { ref, nextTick } from 'vue';

const {modelValue} = defineProps({
    modelValue:Boolean
})

const folderNameInput = ref(null);


const emit = defineEmits(['update:modelValue']);
const page = usePage();
function createFolder(){
    form.parent_id = page.props.folder.id;
    form.post(route('folder.create'),{
        preserveScroll:true,
        onSuccess:()=>{
            closeModal();
            form.reset();

            // Show success notification
        },
        onError:()=>folderNameInput.value.focus()
    });
}

function closeModal(){
    emit('update:modelValue')
    form.clearErrors();
    form.reset();
}

function onShow(){
    nextTick(()=>folderNameInput.value.focus())
}

const form = useForm({
    name:'',
    parent_id:null,
})
</script>

<template>
    <Modal 
        :show="modelValue" 
        max-width="sm"
        @show="onShow"
    >
        <div 
            class="p-6"
        >
            <h2
                class="mt-6"
            >
                Create New Folder
            </h2>
            <div 
                class="mt-6"
            >
                <InputLabel 
                    for="folderName"
                    value="Folder Name"
                />
                <TextInput 
                    type="text"
                    ref="folderNameInput"
                    id="folderName"
                    v-model="form.name"
                    class="mt-1 block w-full"
                    :class="form.errors.name ? 'border-red-500 focus:border-red-500 focus:ring-red-500' : ''"
                    placeholder="Folder Name"
                    @keyup.enter = "createFolder"
                />

                <InputError 
                    :message="form.errors.name"
                    class="mt-2"
                />
            </div>
            <div 
                class="mt-6 flex justify-end"
            >
                <SecondaryButton
                    @click="closeModal"
                >
                    Cancel
                </SecondaryButton>
                <PrimaryButton
                    class="ml-3"
                    @click="createFolder"
                    :class="{'opacity-25':form.processing}"
                    :disable="form.processing"
                >
                    Submit
                </PrimaryButton>
            </div>
        </div>
    </Modal>
</template>