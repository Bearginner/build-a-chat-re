<template>
    <button @click="toggleListen" :class="{'is-listening': isListening}" type="button">
        {{ isListening ? 'Detener' : 'Escuchar' }}
    </button>
</template>

<script setup>
import { ref } from 'vue';

const emit = defineEmits(['update:text']);
const isListening = ref(false);

const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
let recognition = null;

if (SpeechRecognition) {
    recognition = new SpeechRecognition();
    recognition.lang = 'es-ES';
    recognition.continious = false;

    recognition.onstart = (event) => {
        const transcript= event.results[0][0].transcript;
        emit('update:text', transcript);
    };
}

const toggleListen = () => {
    if (isListening.value) {
        recognition.stop();
    } else {
        recognition.start();
    }
};
</script>

<style>
.is_listening {
    background-color: #FF4D4D;
    color: white;
    animation: pulse 1s infinite;
}

@keyframes pulse {
 0% { opacity: 1; }
 50% { opacity: 0.5; }
 100% { opacity: 1; }
}
</style>