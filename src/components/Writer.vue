<template>
  <span>
    <span v-if="currentText">
      <template v-if="$slots['first_word'] && $slots['rest_word']">
        <slot name="first_word" :text="currentTextFirstWord" />
        <slot name="rest_word" :text="currentTextRest" />
      </template>
      <template v-else>
        {{ currentText }}
      </template>
    </span>
    <span v-else>&nbsp;</span>
    <span v-if="showCursor" class="cursor">|</span>
  </span>
</template>

<script lang="ts" setup>
import { ref, watch, onMounted, computed } from 'vue';

/* Super easy, drop in TypeWriter Component for Vue 3
 *
 * Just use like this <TypeWriter :texts="['TEXT1', 'TEXT2']" :eraseDelay="100" :typeDelay="100" :waitTimer="1800" /> */
const props = defineProps(["texts", "eraseDelay", "typeDelay", "waitTimer"]);

const texts = ref(props.texts || []);
const currentIndex = ref(0);
const currentText = ref("");
const showCursor = ref(true); // enable / disable cursor -> could pass this to props aswell if you need it

//start typing
const startTyping = () => {
  // loop through texts
  if (currentIndex.value < texts.value.length) {
    const text = texts.value[currentIndex.value]; //get the text of this element
    let index = 0; // i for looping through the word character by character

    const typingInterval = setInterval(() => {
      // typing interval
      currentText.value = text.slice(0, index);
      index++;

      if (index > text.length) {
        clearInterval(typingInterval);

        setTimeout(() => {
          startErasing();
        }, props.waitTimer || 100); // use timer from props or 100
      }
    }, props.typeDelay || 100); // same here
  }
  if (currentIndex.value == texts.value.length) {
    currentIndex.value = 0; // reset the index
    startTyping(); // start again after array is empty
  }
};

const startErasing = () => {
  const text = texts.value[currentIndex.value];
  let index = text.length;

  const erasingInterval = setInterval(() => {
    currentText.value = text.slice(0, index);
    index--; // same principle but opposite

    if (index < 0) {
      clearInterval(erasingInterval);
      showCursor.value = false; // simulate a blinking cursor

      setTimeout(() => {
        currentIndex.value++;
        currentText.value = "";
        showCursor.value = true;
        startTyping();
      }, props.eraseDelay || 100);
    }
  }, props.eraseDelay || 100);
};

const currentTextFirstWord = computed(() => {
  return currentText.value.split(" ")[0];
});

const currentTextRest = computed(() => {
  return currentText.value.split(" ").slice(1).join(" ");
});

watch(texts, () => {
  currentIndex.value = 0;
  startTyping();
});

onMounted(() => {
  startTyping();
});
</script>

<style scoped>
.cursor {
  animation: blink 0.7s infinite;
}

@keyframes blink {
  0%,
  100% {
    opacity: 1;
  }
  50% {
    opacity: 0;
  }
}
</style>
