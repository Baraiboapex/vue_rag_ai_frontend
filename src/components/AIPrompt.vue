<script setup>
  import { io } from "socket.io-client";
  import { ref, onBeforeUnmount, reactive, onMounted } from "vue";

  import AskButton from "./AskButton/AskButton.vue";
  import RagResponse from "./AskButton/RAGResponse.vue";
  import AboutButton from "./AboutButton/AboutButton.vue";
  import AboutModal from "./AboutButton/AboutModal.vue";
  import DocumentReviewButton from "./DocumentReviewButton/DocumentReviewButton.vue";
  import DocumentReviewModal from "./DocumentReviewButton/DocumentReviewModal.vue";

  const RAG_API_URL = import.meta.env.VITE_VUE_APP_DEV_RAG_API_URL;

  defineProps({
    msg: String 
  });

  const question = ref("");
  const response = ref("");
  const canShowResponse = ref(false);
  const showDownloadDocumentModal = ref(false);
  const showAboutModal = ref(false);
  const responseLoading = ref(true);
  
  const userId = crypto.randomUUID();
  const tabId = crypto.randomUUID();

  console.log(userId);

  const socket = io(RAG_API_URL, {
    transports: ["websocket"], // optional: force WebSocket
    reconnection: true,
    reconnectionAttempts: Infinity,
    timeout: 60000,
    autoConnect:true,
    query:{
      userId:userId,
      tabId:tabId
    }
  });

  onBeforeUnmount(()=>{
    socket.emit("close_rag_ai");
  })

  const loadResponse = () => {
    response.value = "";
    canShowResponse.value = true;
    responseLoading.value = true;
    showAboutModal.value = false;
    showDownloadDocumentModal.value = false;

    socket.emit("sendMessage", {
        sessionId:crypto.randomUUID(),
        question:question.value
    });
  };
  
  const closeResponse = () =>{
    canShowResponse.value = false;
  };

  socket.on("sendResponse", (data) => {
    console.log("INCOMMING DATA : ", data);
    response.value = response.value + data;
    responseLoading.value = false;
  });

  socket.on("connect", () => {
    console.log("Connected to Socket.IO server");
  });

  socket.on("disconnect", () => {
    console.log("Disconnected from server");
  });

  const openAboutModal = ()=>{
    canShowResponse.value = false;
    showDownloadDocumentModal.value = false;
    showAboutModal.value = true;
  };

  const openDownloadDocument = ()=>{
    canShowResponse.value = false;
    showAboutModal.value = false;
    showDownloadDocumentModal.value = true;

  };
</script>

<template>
  <div class="container-fluid">
    <div class="col-12 w-100">
      <h1>
        <svg xmlns="http://www.w3.org/2000/svg" width="70" height="70" fill="currentColor" class="bi bi-robot" viewBox="0 0 16 16">
        <path d="M6 12.5a.5.5 0 0 1 .5-.5h3a.5.5 0 0 1 0 1h-3a.5.5 0 0 1-.5-.5M3 8.062C3 6.76 4.235 5.765 5.53 5.886a26.6 26.6 0 0 0 4.94 0C11.765 5.765 13 6.76 13 8.062v1.157a.93.93 0 0 1-.765.935c-.845.147-2.34.346-4.235.346s-3.39-.2-4.235-.346A.93.93 0 0 1 3 9.219zm4.542-.827a.25.25 0 0 0-.217.068l-.92.9a25 25 0 0 1-1.871-.183.25.25 0 0 0-.068.495c.55.076 1.232.149 2.02.193a.25.25 0 0 0 .189-.071l.754-.736.847 1.71a.25.25 0 0 0 .404.062l.932-.97a25 25 0 0 0 1.922-.188.25.25 0 0 0-.068-.495c-.538.074-1.207.145-1.98.189a.25.25 0 0 0-.166.076l-.754.785-.842-1.7a.25.25 0 0 0-.182-.135"/>
        <path d="M8.5 1.866a1 1 0 1 0-1 0V3h-2A4.5 4.5 0 0 0 1 7.5V8a1 1 0 0 0-1 1v2a1 1 0 0 0 1 1v1a2 2 0 0 0 2 2h10a2 2 0 0 0 2-2v-1a1 1 0 0 0 1-1V9a1 1 0 0 0-1-1v-.5A4.5 4.5 0 0 0 10.5 3h-2zM14 7.5V13a1 1 0 0 1-1 1H3a1 1 0 0 1-1-1V7.5A3.5 3.5 0 0 1 5.5 4h5A3.5 3.5 0 0 1 14 7.5"/>
        </svg>
      </h1>
      <h1>{{ msg }}</h1>
      <div class="d-flex flex-column align-items-start justify-content-start text-start">
        <p>
          To get started using the RAG AI, Ask the RAG AI <strong>"Summarize my contract."</strong>
          To view the full contract, you can download the full document by 
          clicking <strong>"Download Used Document"</strong> button.
        </p>
      </div>
      <textarea type="text" placeholder="Ask me about your contract" v-model="question" class="mt-2 rounded text-light w-100"></textarea>
      <div class="d-flex w-100 flex-column">
        <div class="d-flex flex-row">
          <AskButton @click="loadResponse"/>
          <span>&nbsp;</span>
          <AboutButton @click="openAboutModal"/>
          <span>&nbsp;</span>
          <DocumentReviewButton @click="openDownloadDocument"/>
        </div>
        <div class="d-flex flex-row w-100 mt-4">
          <DocumentReviewModal :isVisible="showDownloadDocumentModal"/>
          <AboutModal :isVisible="showAboutModal"/>
          <RagResponse
            :showResponse="canShowResponse" 
            :responseIsLoading="responseLoading" 
            :response="response"
            @close-response-prompt="closeResponse"
          />
        </div>
      </div>
    </div>
  </div>
</template>