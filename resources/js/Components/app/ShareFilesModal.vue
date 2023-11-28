<script setup>
import { useForm, usePage } from '@inertiajs/vue3';
import InputError from '../InputError.vue';
import InputLabel from '../InputLabel.vue';
import Modal from '../Modal.vue';
import TextInput from '../TextInput.vue';
import SecondaryButton from '../SecondaryButton.vue';
import PrimaryButton from '../PrimaryButton.vue';
import { ref, nextTick } from 'vue';
import { showSuccessNotification } from '@/event-bus';

const props = defineProps({
    modelValue:Boolean,
    allSelected: Boolean,
    selectedIds: Array
})

const emailInput = ref(null);


const emit = defineEmits(['update:modelValue']);
const page = usePage();
function share(){
    form.parent_id = page.props.folder.id
    if (props.allSelected) {
        form.all = true;
        form.ids = [];
    }  else {
        form.ids = props.selectedIds
    }
    const email = form.email
    form.post(route('file.share'), {
        preserveScroll: true,
        onSuccess: () => {
            closeModal()
            form.reset();
            // Show success notification
            showSuccessNotification(`Selected files will be shared to "${email}" if the emails exists in the system`)
        },
        onError: () => emailInput.value.focus()
    })
}

function closeModal(){
    emit('update:modelValue')
    form.clearErrors();
    form.reset();
}

function onShow(){
    nextTick(()=>emailInput.value.focus())
}

const form = useForm({
    email: null,
    all: false,
    ids:[],
    parent_id: null
})
</script>

<template>
    <Modal 
        :show="props.modelValue" 
        max-width="sm"
        @show="onShow"
    >
        <div 
            class="p-6"
        >
            <h2
                class="mt-6"
            >
                Share files
            </h2>
            <div 
                class="mt-6"
            >
                <InputLabel 
                    for="shareEmail"
                    value="Enter Email Adress"
                />
                <TextInput 
                    type="text"
                    ref="emailInput"
                    id="shareEmail"
                    v-model="form.email"
                    class="mt-1 block w-full"
                    placeholder="Enter Email Adress"
                    :class="form.errors.email ? 'border-red-500 focus:border-red-500 focus:ring-red-500' : ''"
                    @keyup.enter = "share"
                />
                <InputError 
                    :message="form.errors.email"
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
                    @click="share"
                    :class="{'opacity-25':form.processing}"
                    :disable="form.processing"
                >
                    Submit
                </PrimaryButton>
            </div>
        </div>
    </Modal>
</template>