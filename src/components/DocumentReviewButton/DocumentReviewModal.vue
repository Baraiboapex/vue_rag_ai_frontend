<script setup>
    import {ref, watch, toRef} from "vue";

    const props = defineProps([
        "isVisible"
    ]);

    const documentLoading = ref(false);
    const isVisibleRef = toRef(props, "isVisible");

    watch(isVisibleRef, async (next,prev)=>{
        console.log("TEST : ", prev,next);
        if(next === true){
            try{
                console.log("TEST!");
                documentLoading.value = true;
            
                const getDocument = await fetch("./SampleContract-Shuttle.pdf");
                const documentBlob = await getDocument.blob();
                const finalDocument = documentBlob;
                const url = URL.createObjectURL(finalDocument);
                const link = document.createElement("a");
                
                link.href = url;
                link.download = "rag-contract-document.pdf";
                link.click();
                URL.revokeObjectURL(url);

                documentLoading.value = false;
            }catch(err){
                console.error(err);
            }
        }
    })
    
</script>
<template>
    <div v-if="props.isVisible" class="card p-2 d-flex flex-column justify-content-center align-items-center w-100">
        <div v-if="documentLoading" class="p-2 d-flex flex-column justify-content-center align-items-center">
            <div class="spinner-border text-dark" role="status">
                <span class="visually-hidden">Loading...</span>
            </div>
            <h3>Loading Document...</h3>
        </div>
        <div v-else class="p-2 d-flex flex-column justify-content-center align-items-center">
            <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-check-circle" viewBox="0 0 16 16">
                <path d="M8 15A7 7 0 1 1 8 1a7 7 0 0 1 0 14m0 1A8 8 0 1 0 8 0a8 8 0 0 0 0 16"/>
                <path d="m10.97 4.97-.02.022-3.473 4.425-2.093-2.094a.75.75 0 0 0-1.06 1.06L6.97 11.03a.75.75 0 0 0 1.079-.02l3.992-4.99a.75.75 0 0 0-1.071-1.05"/>
            </svg>
            <h3>Document Loaded!</h3>
        </div>
    </div>
</template>