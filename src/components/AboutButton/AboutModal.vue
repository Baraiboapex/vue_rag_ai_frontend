<script setup>
    import {ref} from "vue";
    const props = defineProps({isVisible:Boolean});
</script>
<template>
    <div v-if="props.isVisible" class="card p-2 d-flex justify-content-start align-items-start w-100">
        <h3>What is a RAG ai?</h3>
        <p class="text-left">
            A RAG AI (Retrieval-Augmented Generation) is special type 
            of AI that is specifically tailored to search domain-specific 
            knowledge through the utilization of various documents an 
            organization supplies it with. This could
            be PDF files, DOCX files, or even Excel spread sheets! If
            it can be represented as text, then it can find needed information on the document! 
            For this example, I used a highly verbose legal document
            to give a pretrained model some context to work with. The model
            will only answer questions specific to the document however, and 
            if a user asks any questions outside of the domain of the AI, it 
            will respond saying that it cannot answer that question.

            It really is exciting to think of the possibilities that tools
            such as these can bring to any organization!
        </p>
    </div>
</template>