<template>
    <button @click="toggleListen" :class="['mic-btn', {'is-listening': isListening}]" type="button">
        <Mic v-if = "!isListening" :size = "20" color = "#FFFFFF" />
        <Square v-else :size = "20"  color = "#FFFFFF" />
        <div v-if = "isListening" class = "pulse-mic"> </div>
    </button>
</template>

<script setup>
import { ref } from 'vue';
import { Mic, Square } from 'lucide-vue-next';

const handleVoiceText = (text) => {
    newMessage.value = text;
};
const emit = defineEmits(['update:text']);
const isListening = ref(false);

const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
let recognition = null;

if (SpeechRecognition) {
    recognition = new SpeechRecognition();
    recognition.lang = 'es-ES';
    recognition.continious = true;
    recognition.interimResults = true;

    recognition.onstart = () => {
        isListening.value= true;
    }
    recognition.onend = () => {
        isListening.value = false;
    }

    recognition.onresult = (event) => {
        let transcript = '';
        for (let i = event.resultIndex; i < event.results.length; i++) {
          if (event.results[i].isFinal) {
             transcript += event.results[i][0].transcript;
          } else {
             transcript += event.results[i][0].transcript;
          }  
       } 
       emit('update:text', transcript);
    };

    recognition.onerror = (event) => {
        console.error("Error de reconocimiento de voz", event.error);
        isListening.value = false;
    }
}

const toggleListen = () => {
    if (isListening.value) {
        recognition.stop();
    } else {
        recognition.start();
    }
};
</script>

<style scoped>
.mic-btn {
    position: relative;
    width: 45px;
    height: 45px;
    background-color: #0066FF;
    border-radius: 14px;
    display: flex;
    align-items: center;
    justify-content: center;
    cursor: pointer;
    transition: all 0.3s ease;
}

.mic-btn:hover{
    background-color: #0052CC;
    transform: translateY(-1px);
}

.is-listening {
    background-color: #EF4444;
    box-shadow: 0 0 15px rgba(239, 68, 68, 0.5);
}

.pulse-mic {
    position: absolute;
    width: 100%;
    height: 100%;
    border: 2px solid #EF4444;
    border-radius: 10px;
    animation: ripple 1.5s infinite ease-out;
}

@keyframes ripple {
    0% {
        transform: scale(1);
        opacity: 0.8;
    }

    100% {
        transform: scale(1.6);
        opacity: 0;
    }
}
</style>