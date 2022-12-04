<template>
  <div class="w-full h-full flex justify-center items-center">
    <span v-if="isLoading"></span>
    <div v-else-if="!isLoading && joke" class="flex flex-col gap-4">
      <span class="bg-green text-2xl p-4 rounded-lg text-zinc-100 w-9/12">{{ joke.setup }}</span>
      <Transition name="slide-fade">
        <span v-if="isReveal" class="self-end bg-amber-600 text-2xl p-4 rounded-lg text-zinc-100 w-9/12">{{ joke.delivery }}</span>
      </Transition>
      <div class="flex gap-4 relative pt-6">
        <Transition name="slide-up" mode="out-in">
          <button v-if="!isReveal" @click="onReveal" class="absolute w-full text-lg bg-gray-dark text-white p-2 rounded-lg">Tell Me!</button>
          <button v-else @click="loadJoke" class="absolute w-full text-lg bg-gray-dark text-white p-2 rounded-lg">Another</button>
        </Transition>
      </div>
    </div>
  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue';

const isLoading = ref(false)
const isReveal = ref(false)
const joke = ref()

const loadJoke = async () => {
  isLoading.value = true
  isReveal.value = false
  try {
    const res = await (await fetch('https://v2.jokeapi.dev/joke/christmas')).json();
    joke.value = !res.error ? res : 'Not Found';
  } catch (error) {
    console.log(error);
  } finally {
    isLoading.value = false
  }
}

const onReveal = () => {
  isReveal.value = true
}

onMounted(() => {
  loadJoke();
})
</script>

<style>
.slide-fade-enter-active {
  transition: all 0.3s ease-out;
}

.slide-fade-enter-from {
  transform: translateX(20px);
  opacity: 0;
}

.slide-up-enter-active,
.slide-up-leave-active {
  transition: all 0.25s ease-out;
}

.slide-up-enter-from {
  opacity: 0;
  transform: translateY(-30px);
}

.slide-up-leave-to {
  opacity: 0;
  transform: translateY(0px);
}
</style>
