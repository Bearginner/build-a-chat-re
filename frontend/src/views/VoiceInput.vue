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
    recognition.interimResults = false;

    recognition.onstart = () => {
        isListening.value= true;
    }
    recognition.onend = () => {
        isListening.value = false;
    }

    recognition.onresult = (event) => {
        const transcript = event.results[0][0].transcript;
        if (transcript) {
             emit('update:text', transcript);
        } 
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

</style>