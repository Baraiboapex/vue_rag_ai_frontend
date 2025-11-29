<script setup>
    const props = defineProps({
        showResponse:Boolean,
        responseIsLoading:Boolean,
        response:String
    });

    const emit = defineEmits([
        "close-response-prompt"
    ]);

    const closeResponse = () => {
        emit("close-response-prompt");
    };
</script>
<template>
    <div v-if="props.showResponse" class="d-flex card flex-column justify-content-center align-items-center p-2 w-100">
        <div class="d-flex flex-row flex-row w-100">
            <button @click="closeResponse" class="btn btn-link text-decoration-none p-1 m-0">
                Close
                <svg xmlns="http://www.w3.org/2000/svg" width="17" height="17" fill="currentColor" class="bi bi-x" viewBox="0 0 16 16">
                    <path d="M4.646 4.646a.5.5 0 0 1 .708 0L8 7.293l2.646-2.647a.5.5 0 0 1 .708.708L8.707 8l2.647 2.646a.5.5 0 0 1-.708.708L8 8.707l-2.646 2.647a.5.5 0 0 1-.708-.708L7.293 8 4.646 5.354a.5.5 0 0 1 0-.708"/>
                </svg>
            </button>
        </div>
        <div v-if="props.responseIsLoading" class="d-flex flex-column justify-content-center align-items-center">
            <span><strong>IMPORTANT NOTE!</strong> I do not have the funds to access more GPU to boost AI performance, please be patient :)</span>
            <div class="spinner-border text-dark" role="status">
                <span class="visually-hidden">Loading...</span>
            </div>
            <h3>Loading Response</h3>
        </div>
        <div v-else class="text-start">
            <h4>Response</h4>
            <p>{{props.response}}</p>
        </div>
    </div>
</template>