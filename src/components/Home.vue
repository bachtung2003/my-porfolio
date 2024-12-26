<script setup>
import { ref, computed, onMounted } from "vue";
import arrowDown from "@/assets/arrow-down.mp4";
import thumbImg from "@/assets/thumb-pic.jpg";

const isSecondContentVisible = ref(false);
const isThirdContentVisible = ref(false);
const scrollY = ref(0); // Tracks vertical scroll position
// Compute the opacity based on scrollY
const contentOpacity = computed(() => {
  const maxOpacityScroll = 500; // Maximum scroll height for opacity transition
  const clampedScroll = Math.min(Math.max(scrollY.value, 0), maxOpacityScroll); // Clamp scroll value between 0 and maxOpacityScroll
  return 1 - clampedScroll / maxOpacityScroll; // Decrease opacity from 1 to 0
});
// Apply the opacity logic to all spans on scroll
const applySpanOpacity = () => {
  const spans = document.querySelectorAll("#about span");
  spans.forEach((span) => {
    span.style.opacity = calculateSpanOpacity(span);
  });
};

onMounted(() => {
  // Simulate a 5-second loading time

  // Delay the second content's visibility by 2 seconds
  setTimeout(() => {
    isSecondContentVisible.value = true;

    // Delay the third content's visibility by 2 seconds
    setTimeout(() => {
      isThirdContentVisible.value = true;
    }, 2000);
  }, 2000);
  // Track scroll
  window.addEventListener("scroll", () => {
    scrollY.value = window.scrollY;
    applySpanOpacity();
  });

  // Apply opacity on initial load
  applySpanOpacity();
});
</script>

<template>
  <div class="flex z-1 h-screen w-full relative">
    <!-- Fade-out Content -->
    <div class="fixed p-5 flex flex-col justify-between h-full w-full">
      <div
        class="flex flex-1 justify-between"
        :style="{ opacity: contentOpacity }"
      >
        <div class="animate-slide-in-delay-left">
          <span class="font-extrabold text-8xl">SOFTWARE</span>
          <br />
          <span class="font-extrabold text-8xl">DEVELOPER</span>
          <br />
          <span class="flex justify-end font-light text-4xl">
            - FRONT END SPECIALIST
          </span>
        </div>
        <div class="animate-slide-down">
          <div
            class="h-full max-h-[35rem] w-[25rem] bg-no-repeat bg-cover relative"
            style="background-position: 50%"
          >
            <img :src="thumbImg" alt="" />
          </div>
        </div>
      </div>

      <!-- 2nd Main Content -->
      <div
        v-if="isSecondContentVisible"
        class="flex flex-1 bottom-4 right-4 left-4 items-end text-right justify-between"
        :style="{ opacity: contentOpacity }"
      >
        <div class="flex flex-col items-center animate-slide-in-delay-left">
          <video
            :src="arrowDown"
            autoplay
            loop
            muted
            class="w-10 h-auto"
          ></video>
          <span class="font-light text-2xl animate-slide-down">SCROLL</span>
        </div>
        <div class="flex flex-col animate-slide-in-delay-right">
          <span
            v-if="isThirdContentVisible"
            class="font-light text-4xl animate-slide-down"
          >
            - WEB DESIGNING CAPABLE
          </span>
          <span class="font-extrabold text-8xl">TRAN BACH TUNG</span>
        </div>
      </div>
    </div>
  </div>
</template>

<style></style>
