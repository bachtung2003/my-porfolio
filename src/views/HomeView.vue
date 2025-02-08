<script setup>
import { ref, computed, onMounted } from "vue";
import logoVid from "@/assets/logo.mp4";
import arrowDown from "@/assets/arrow-down.mp4";
import thumbImg from "@/assets/thumb-pic.jpg";
import thumbImg2 from "@/assets/thumb-pic-2.jpg";
import rotateImg from "@/assets/3.png";
import docker from "@/assets/docker.png";
import figma from "@/assets/figma.png";
import git from "@/assets/git.png";
import mongodb from "@/assets/mongodb.png";
import mysql from "@/assets/mysql.png";
import nextjs from "@/assets/nextjs.png";
import nodejs from "@/assets/node-js.png";
import postman from "@/assets/postman.png";
import react from "@/assets/react.png";
import tailwind from "@/assets/tailwind.png";
import vue from "@/assets/vue.png";
import ScrollReveal from "scrollreveal";

ScrollReveal({ reset: true, distance: "60px", duration: 2500, delay: 400 });

ScrollReveal().reveal(".project", { delay: 500, origin: "bottom" });

// State to track if the loading is complete
const isLoading = ref(true);
const isSecondContentVisible = ref(false);
const isThirdContentVisible = ref(false);
const scrollY = ref(0); // Tracks vertical scroll position
const isFooterVisible = ref(false); // Tracks if the footer is in view
const isMax2xl = ref(false);

const updateScreenSize = () => {
  isMax2xl.value = window.innerWidth <= 1536; // 1536px is the max width for 2xl in Tailwind
};
// Compute the scaleY value based on scrollY
const scaleY = computed(() => {
  const maxScroll = 500; // Maximum scroll height for scaling effect
  const clampedScroll = Math.min(Math.max(scrollY.value, 0), maxScroll); // Clamp scroll value between 0 and maxScroll
  return clampedScroll / maxScroll; // Scale between 0 and 1
});

const scaleY2 = computed(() => {
  if (!isFooterVisible.value) return 1; // If footer is not visible, scaleY is 0

  const maxScroll = 7055.5; // Maximum scroll value at the end of the page
  const minScroll = maxScroll - 500; // Start of the scaling effect
  const clampedScroll = Math.min(Math.max(scrollY.value, minScroll), maxScroll); // Clamp scrollY between minScroll and maxScroll

  return (maxScroll - clampedScroll) / 500; // Scale linearly between 1 (at minScroll) and 0 (at maxScroll)
});

// Compute the opacity based on scrollY
const contentOpacity = computed(() => {
  const maxOpacityScroll = 500; // Maximum scroll height for opacity transition
  const clampedScroll = Math.min(Math.max(scrollY.value, 0), maxOpacityScroll); // Clamp scroll value between 0 and maxOpacityScroll
  return 1 - clampedScroll / maxOpacityScroll; // Decrease opacity from 1 to 0
});

// Function to calculate opacity for spans based on their distance from the viewport center
const calculateSpanOpacity = (element) => {
  const rect = element.getBoundingClientRect();
  const elementCenter = rect.top + rect.height / 2;
  const viewportCenter = window.innerHeight / 2;
  const distance = Math.abs(elementCenter - viewportCenter);

  // Adjust these values to control the range and behavior
  const maxDistance = window.innerHeight / 2;
  const minOpacity = 0.2;
  const maxOpacity = 1;

  if (distance >= maxDistance) return minOpacity;
  return maxOpacity - (distance / maxDistance) * (maxOpacity - minOpacity);
};

const applyGlowEffect = () => {
  const spans = document.querySelectorAll("#about span");
  const viewportCenter = window.innerHeight / 2;

  spans.forEach((span) => {
    const rect = span.getBoundingClientRect();
    const elementCenter = rect.top + rect.height / 2;
    const distance = Math.abs(elementCenter - viewportCenter);

    const maxDistance = viewportCenter; // Maximum distance for glow to be visible
    const intensity = Math.max(0, 1 - distance / maxDistance); // Calculate glow intensity

    // Apply the glow effect
    if (intensity > 0) {
      span.style.textShadow = `0 0 ${
        20 * intensity
      }px rgba(255, 255, 255, ${intensity})`;
      // span.style.color = `rgba(255, 255, 255, ${0.2 + intensity * 0.8})`; // Gradually make it white
    } else {
      span.style.textShadow = "none";
      span.style.color = ""; // Reset color
    }
  });
};

// Apply the opacity logic to all spans on scroll
const applySpanOpacity = () => {
  const spans = document.querySelectorAll("#about span");
  spans.forEach((span) => {
    span.style.opacity = calculateSpanOpacity(span);
  });
};

const translateYValue = ref(0); // Initial Y translation

const updateTranslateY = () => {
  const skillImage = document.querySelector("#skill-image");
  if (!skillImage) return;

  const rect = skillImage.getBoundingClientRect();
  const viewportHeight = window.innerHeight;

  // Check when the element is within the viewport
  if (rect.top < viewportHeight && rect.bottom > 0) {
    const progress = Math.min(
      Math.max((viewportHeight - rect.top) / viewportHeight, 0),
      1
    );
    translateYValue.value = -50 * progress; // Scale the translation to -50%
  }
};

const translateXValue = ref(80); // Initial X translation

const updateTranslateX = () => {
  const skillContainer = document.querySelector("#skill"); // Parent container
  const skillSpan = document.querySelector("#skill-span"); // Target span
  if (!skillContainer || !skillSpan) return;

  const rect = skillContainer.getBoundingClientRect();
  const viewportHeight = window.innerHeight;

  // Check when the container is within the viewport
  if (rect.top < viewportHeight && rect.bottom > 0) {
    const progress = Math.min(
      Math.max((viewportHeight - rect.top) / viewportHeight, 0),
      1
    );
    translateXValue.value = 80 + (300 - 80) * progress; // Scale the translation from 80px to 250px
  }
};

const isSkillFixed = ref(false); // Track if the skill section is fixed

const updateSkillFixedState = () => {
  const skillContainer = document.querySelector("#skill");
  if (!skillContainer) return;

  const rect = skillContainer.getBoundingClientRect();
  const viewportHeight = window.innerHeight;

  // Check if the skill container reaches the fixed position
  isSkillFixed.value = rect.top <= 0 && rect.bottom >= viewportHeight;
};

onMounted(() => {
  updateScreenSize(); // Check initial screen size
  window.addEventListener("resize", updateScreenSize); // Update on resize

  // Add scroll listener for skill section visibility
  window.addEventListener("scroll", updateSkillFixedState);
  updateSkillFixedState(); // Initial state check
  // Add scroll listener to update the translateX value
  window.addEventListener("scroll", updateTranslateX);
  updateTranslateX(); // Initial call
  // Add scroll listener to update the translateY value
  window.addEventListener("scroll", updateTranslateY);
  updateTranslateY(); // Initial call
  window.addEventListener("scroll", applyGlowEffect);
  applyGlowEffect(); // Apply glow effect on initial load

  // Simulate a 5-second loading time
  setTimeout(() => {
    isLoading.value = false;

    // Delay the second content's visibility by 2 seconds
    setTimeout(() => {
      isSecondContentVisible.value = true;

      // Delay the third content's visibility by 2 seconds
      setTimeout(() => {
        isThirdContentVisible.value = true;
        // Observe the footer element for visibility changes
        const footer = document.querySelector("footer");
        const observer = new IntersectionObserver(
          ([entry]) => {
            isFooterVisible.value = entry.isIntersecting; // Update visibility
          },
          { threshold: 0.1 } // Trigger when 10% of the footer is visible
        );

        observer.observe(footer);
      }, 2000);
    }, 2000);
  }, 3000);

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
  <div class="min-h-screen bg-white relative overflow-hidden">
    <!-- Loading Screen -->
    <div
      v-if="isLoading"
      class="min-h-screen flex items-center justify-center w-full h-full"
    >
      <video :src="logoVid" autoplay muted class="w-2/5 h-auto"></video>
    </div>

    <!-- Main Content -->
    <div v-else>
      <div
        class="w-full fixed left-0 top-[-10px] bg-black will-change-transform max-md:hidden"
        :style="{
          transformOrigin: '50% 0%',
          height: '50vh',
          transform: `translate3d(0px, 0px, 0px) scale(1, ${scaleY})`,
          zIndex: scaleY == 1 ? 1 : 2,
        }"
      ></div>
      <div class="flex z-1 h-screen w-full relative">
        <!-- Fade-out Content -->
        <div class="fixed p-5 flex flex-col justify-between h-full w-full">
          <div
            class="flex flex-1 lg:justify-between flex-col items-center lg:flex-row lg:items-start"
            :style="{ opacity: contentOpacity }"
          >
            <div class="max-sm:flex max-sm:gap-1 animate-slide-in-delay-left">
              <span class="font-extrabold sm:text-8xl">SOFTWARE</span>
              <br />
              <span class="font-extrabold sm:text-8xl">DEVELOPER</span>
              <br />
              <span
                class="flex justify-end font-light sm:text-4xl max-lg:pl-14"
              >
                - FRONT END SPECIALIST
              </span>
            </div>
            <div class="max-sm:pt-20 animate-slide-down">
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
              <span class="font-light sm:text-2xl animate-slide-down"
                >SCROLL</span
              >
            </div>
            <div class="flex flex-col animate-slide-in-delay-right">
              <span
                v-if="isThirdContentVisible"
                class="font-light sm:text-4xl animate-slide-down"
              >
                - WEB DESIGNING CAPABLE
              </span>
              <span class="font-extrabold max-lg:text-4xl text-8xl"
                >TRAN BACH TUNG</span
              >
            </div>
          </div>
        </div>
      </div>

      <div class="relative z-[3] bg-black">
        <div class="relative z-[1]">
          <div class="pb-48 relative">
            <div class="px-8 mb-16" id="about">
              <div class="pt-80 w-[70rem] m-auto pb-40 relative">
                <p
                  class="text-white max-xl:w-min max-lg:text-4xl max-lg:leading-loose max-xl:text-6xl max-xl:leading-normal max-2xl:text-7xl text-9xl font-semibold"
                >
                  <span
                    >Over the years I have spent time converting designs</span
                  >
                  <span>into pixel-perfect,</span>
                  <span>performant,</span>
                  <span>accessible</span>
                  <span>and responsive applications/websites.</span>
                  <span
                    >I have always been excited about the entire development
                    spectrum,</span
                  >
                  <span>so I frequently engage in backend.</span>
                  <span>Well what can I say,</span>
                  <span>I sincerely simply love working on ambitious </span>
                  <span>projects with positive people</span>
                  <span>in a conducive work environment.</span>
                </p>
              </div>
            </div>
            <div class="block min-h-[120vh] mt-24" id="skill">
              <div
                class=""
                style="
                  order: 0;
                  place-self: auto;
                  grid-area: auto;
                  z-index: auto;
                  float: none;
                  flex-shrink: 1;
                  display: flex;
                  margin: 0px;
                  inset: auto;
                  position: relative;
                  flex-basis: auto;
                  overflow: visible;
                  box-sizing: border-box;
                  width: 1680px;
                  height: 660px;
                  padding: 0px;
                "
              >
                <div
                  style="
                    transform: translate(0px, 0px);
                    inset: 0px auto auto 0px;
                    margin-top: 98px;
                    max-width: 1680px;
                    width: 1680px;
                    max-height: 660px;
                    height: 660px;
                    padding: 0px;
                  "
                  :style="{
                    position: 'relative',
                  }"
                >
                  <div
                    id="skill-image"
                    style="width: 29vw"
                    :style="{ transform: `translateY(${translateYValue}%)` }"
                    class="max-sm:hidden absolute h-[69vh] max-w-[68rem] max-h-[90rem] top-1/2 bg-cover bg-no-repeat bg-[url('../assets/thumb-pic-2.jpg')] bg-center"
                  ></div>
                  <div class="max-w-fit ml-16 max-sm:ml-[-15rem]">
                    <p class="text-white text-9xl font-semibold mb-28">
                      <span
                        id="skill-span"
                        style="transform: translate(80px, 80px)"
                        :style="{
                          transform: `translateX(${translateXValue}px)`,
                        }"
                        class="block"
                        ><p>Skills</p></span
                      >
                    </p>
                    <div
                      class="grid grid-cols-3 gap-16 ml-64 max-2xl:justify-items-end max-lg:flex max-sm:flex-col"
                    >
                      <div class="max-2xl:hidden"></div>
                      <div>
                        <section class="mb-16">
                          <h3 class="text-white text-2xl font-semibold mb-9">
                            LANGUAGES
                          </h3>
                          <ul class="text-white">
                            <li>HTML</li>
                            <li>CSS</li>
                            <li>Typescript</li>
                          </ul>
                        </section>
                        <section>
                          <h3 class="text-white text-2xl font-semibold mb-9">
                            MISCELLANEOUS
                          </h3>
                          <ul class="text-white">
                            <li>Git/ Github</li>
                            <li>Postman</li>
                            <li>Figma</li>
                            <li>Docker</li>
                          </ul>
                        </section>
                      </div>
                      <div>
                        <section class="mb-16">
                          <h3
                            style="opacity: 1"
                            class="text-white text-2xl font-semibold mb-9 uppercase"
                          >
                            Frameworks/ Libraries/ DBMS
                          </h3>
                          <ul class="grid grid-cols-2 gap-x-16 text-white">
                            <li>React.js</li>
                            <li>Next.js</li>
                            <li>Node.js</li>
                            <li>Express.js</li>
                            <li>Vue.js</li>
                            <li>Tailwind CSS</li>
                            <li>MySQL</li>
                            <li>MongoDB</li>
                          </ul>
                        </section>
                        <section class="mb-16">
                          <h3
                            style="opacity: 1"
                            class="text-white text-2xl font-semibold mb-9"
                          >
                            SOFT SKILLS
                          </h3>
                          <ul class="grid grid-cols-2 gap-x-16 text-white">
                            <li>Time Management</li>
                            <li>Teamwork</li>
                            <li>Problem‐solving</li>
                            <li>Documentation</li>
                            <li>Communication</li>
                          </ul>
                        </section>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
              <div
                class="slider mt-[16rem] max-sm:mt-[40rem]"
                style="--width: 140px; --height: 80px; --quantity: 11"
              >
                <div class="list bg-white">
                  <div class="item" style="--position: 1">
                    <img :src="docker" alt="" />
                  </div>
                  <div class="item" style="--position: 2">
                    <img :src="figma" alt="" />
                  </div>
                  <div class="item" style="--position: 3">
                    <img :src="git" alt="" />
                  </div>
                  <div class="item" style="--position: 4">
                    <img :src="mongodb" alt="" />
                  </div>
                  <div class="item" style="--position: 5">
                    <img :src="mysql" alt="" />
                  </div>
                  <div class="item" style="--position: 6">
                    <img :src="nextjs" alt="" />
                  </div>
                  <div class="item" style="--position: 7">
                    <img :src="nodejs" alt="" />
                  </div>
                  <div class="item" style="--position: 8">
                    <img :src="postman" alt="" />
                  </div>
                  <div class="item" style="--position: 9">
                    <img :src="react" alt="" />
                  </div>
                  <div class="item" style="--position: 10">
                    <img :src="tailwind" alt="" />
                  </div>
                  <div class="item" style="--position: 11">
                    <img :src="vue" alt="" />
                  </div>
                </div>
              </div>
            </div>

            <div
              class="text-white items-center flex flex-col justify-center max-sm:mt-20 max-lg:mt-10"
            >
              <img
                :src="rotateImg"
                alt=""
                height="400"
                width="400"
                class="autoRotate"
              />
              <p
                class="text-white text-9xl font-semibold mt-28 project max-md:text-6xl"
                id="project"
              >
                PROJECTS
              </p>
              <p class="mt-7 font-light italic">
                - A list of projects I have been working on or built -
              </p>
              <div class="mt-7 grid grid-cols-2 gap-8 max-md:grid-cols-1">
                <div class="md p-4" style="max-width: 544px">
                  <div
                    class="h-full transform overflow-hidden rounded-md border-2 border-solid border-gray-200 bg-transparent bg-opacity-20 transition duration-500 hover:scale-105 hover:rounded-md hover:border-primary-500 hover:bg-gray-600 dark:border-gray-700 dark:hover:border-primary-500 dark:hover:bg-gray-800"
                  >
                    <div class="p-6">
                      <div class="flex flex-row items-center justify-between">
                        <div class="my-2">
                          <svg
                            xmlns="http://www.w3.org/2000/svg"
                            viewBox="0 0 24 24"
                            fill="#fff"
                            stroke="currentColor"
                            class="h-10 w-10 text-primary-color-500 dark:text-primary-color-dark-500"
                          >
                            <path
                              d="M22 19a2 2 0 0 1-2 2H4a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h5l2 3h9a2 2 0 0 1 2 2z"
                            ></path>
                          </svg>
                        </div>
                        <div class="flex flex-row justify-between">
                          <div class="mx-1.5"></div>
                          <div class="mx-1.5">
                            <a
                              href="https://github.com/bachtung2003/tutor-buddy"
                              class="text-sm text-white transition duration-200 hover:rotate-180 hover:text-gray-600"
                            >
                              <span class="sr-only">github</span>
                              <svg
                                stroke="#fff"
                                fill="#fff"
                                stroke-width="0"
                                viewBox="0 0 1024 1024"
                                class="text-white hover:text-primary-color-500 dark:text-white dark:hover:text-primary-color-dark-500 h-10 w-10 border rounded-full"
                                height="1em"
                                width="1em"
                                xmlns="http://www.w3.org/2000/svg"
                              >
                                <path
                                  d="M511.6 76.3C264.3 76.2 64 276.4 64 523.5 64 718.9 189.3 885 363.8 946c23.5 5.9 19.9-10.8 19.9-22.2v-77.5c-135.7 15.9-141.2-73.9-150.3-88.9C215 726 171.5 718 184.5 703c30.9-15.9 62.4 4 98.9 57.9 26.4 39.1 77.9 32.5 104 26 5.7-23.5 17.9-44.5 34.7-60.8-140.6-25.2-199.2-111-199.2-213 0-49.5 16.3-95 48.3-131.7-20.4-60.5 1.9-112.3 4.9-120 58.1-5.2 118.5 41.6 123.2 45.3 33-8.9 70.7-13.6 112.9-13.6 42.4 0 80.2 4.9 113.5 13.9 11.3-8.6 67.3-48.8 121.3-43.9 2.9 7.7 24.7 58.3 5.5 118 32.4 36.8 48.9 82.7 48.9 132.3 0 102.2-59 188.1-200 212.9a127.5 127.5 0 0 1 38.1 91v112.5c.8 9 0 17.9 15 17.9 177.1-59.7 304.6-227 304.6-424.1 0-247.2-200.4-447.3-447.5-447.3z"
                                ></path>
                              </svg>
                            </a>
                          </div>
                        </div>
                      </div>
                      <h2
                        class="mb-3 text-2xl font-bold leading-8 tracking-tight"
                      >
                        TutorBuddy
                      </h2>
                      <p
                        class="prose mb-3 max-w-none text-white dark:text-gray-400"
                      >
                        TutorBuddy is a web-based tutoring platform designed to
                        enhance learning experiences for students and simplify
                        content delivery for teachers. Built with modern web
                        technologies, TutorBuddy ensures usability, scalability,
                        and security.
                      </p>
                      <div class="flex flex-row justify-between">
                        <div class="text-sm text-gray-400">
                          Next.js • TypeScript • MySQL
                        </div>
                      </div>
                    </div>
                  </div>
                </div>
                <div class="md p-4" style="max-width: 544px">
                  <div
                    class="h-full transform overflow-hidden rounded-md border-2 border-solid border-gray-200 bg-transparent bg-opacity-20 transition duration-500 hover:scale-105 hover:rounded-md hover:border-primary-500 hover:bg-gray-600 dark:border-gray-700 dark:hover:border-primary-500 dark:hover:bg-gray-800"
                  >
                    <div class="p-6">
                      <div class="flex flex-row items-center justify-between">
                        <div class="my-2">
                          <svg
                            xmlns="http://www.w3.org/2000/svg"
                            viewBox="0 0 24 24"
                            fill="#fff"
                            stroke="currentColor"
                            class="h-10 w-10 text-primary-color-500 dark:text-primary-color-dark-500"
                          >
                            <path
                              d="M22 19a2 2 0 0 1-2 2H4a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h5l2 3h9a2 2 0 0 1 2 2z"
                            ></path>
                          </svg>
                        </div>
                        <div class="flex flex-row justify-between">
                          <div class="mx-1.5">
                            <a
                              class="text-sm text-gray-500 transition duration-200 hover:rotate-180 hover:text-gray-600"
                              target="_blank"
                              rel="noopener noreferrer"
                              href="https://ahamay.vercel.app/"
                              ><span class="sr-only">external</span
                              ><svg
                                stroke="#fff"
                                fill="none"
                                stroke-width="2"
                                viewBox="0 0 24 24"
                                stroke-linecap="round"
                                stroke-linejoin="round"
                                class="text-gray-700 hover:text-primary-color-500 dark:text-gray-200 dark:hover:text-primary-color-dark-500 h-10 w-10"
                                height="1em"
                                width="1em"
                                xmlns="http://www.w3.org/2000/svg"
                              >
                                <path
                                  d="M18 13v6a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V8a2 2 0 0 1 2-2h6"
                                ></path>
                                <polyline points="15 3 21 3 21 9"></polyline>
                                <line x1="10" y1="14" x2="21" y2="3"></line>
                              </svg>
                            </a>
                          </div>
                          <div class="mx-1.5">
                            <a
                              href="https://github.com/bachtung2003/motor-service-website"
                              class="text-sm text-white transition duration-200 hover:rotate-180 hover:text-gray-600"
                            >
                              <span class="sr-only">github</span>
                              <svg
                                stroke="#fff"
                                fill="#fff"
                                stroke-width="0"
                                viewBox="0 0 1024 1024"
                                class="text-white hover:text-primary-color-500 dark:text-white dark:hover:text-primary-color-dark-500 h-10 w-10 border rounded-full"
                                height="1em"
                                width="1em"
                                xmlns="http://www.w3.org/2000/svg"
                              >
                                <path
                                  d="M511.6 76.3C264.3 76.2 64 276.4 64 523.5 64 718.9 189.3 885 363.8 946c23.5 5.9 19.9-10.8 19.9-22.2v-77.5c-135.7 15.9-141.2-73.9-150.3-88.9C215 726 171.5 718 184.5 703c30.9-15.9 62.4 4 98.9 57.9 26.4 39.1 77.9 32.5 104 26 5.7-23.5 17.9-44.5 34.7-60.8-140.6-25.2-199.2-111-199.2-213 0-49.5 16.3-95 48.3-131.7-20.4-60.5 1.9-112.3 4.9-120 58.1-5.2 118.5 41.6 123.2 45.3 33-8.9 70.7-13.6 112.9-13.6 42.4 0 80.2 4.9 113.5 13.9 11.3-8.6 67.3-48.8 121.3-43.9 2.9 7.7 24.7 58.3 5.5 118 32.4 36.8 48.9 82.7 48.9 132.3 0 102.2-59 188.1-200 212.9a127.5 127.5 0 0 1 38.1 91v112.5c.8 9 0 17.9 15 17.9 177.1-59.7 304.6-227 304.6-424.1 0-247.2-200.4-447.3-447.5-447.3z"
                                ></path>
                              </svg>
                            </a>
                          </div>
                        </div>
                      </div>
                      <h2
                        class="mb-3 text-2xl font-bold leading-8 tracking-tight"
                      >
                        Ahamay
                      </h2>
                      <p
                        class="prose mb-3 max-w-none text-white dark:text-gray-400"
                      >
                        This project involves the development of a web portal
                        that allows users to book motor servicing appointments
                        from the comfort of their homes. The platform is built
                        using Vue.js for the frontend and MongoDB as the backend
                        database, ensuring a modern, responsive, and efficient
                        user experience.
                      </p>
                      <div class="flex flex-row justify-between">
                        <div class="text-sm text-gray-400">
                          Vue.js • PrimeVue • MongoDB
                        </div>
                      </div>
                    </div>
                  </div>
                </div>
                <div class="md p-4" style="max-width: 544px">
                  <div
                    class="h-full transform overflow-hidden rounded-md border-2 border-solid border-gray-200 bg-transparent bg-opacity-20 transition duration-500 hover:scale-105 hover:rounded-md hover:border-primary-500 hover:bg-gray-600 dark:border-gray-700 dark:hover:border-primary-500 dark:hover:bg-gray-800"
                  >
                    <div class="p-6">
                      <div class="flex flex-row items-center justify-between">
                        <div class="my-2">
                          <svg
                            xmlns="http://www.w3.org/2000/svg"
                            viewBox="0 0 24 24"
                            fill="#fff"
                            stroke="currentColor"
                            class="h-10 w-10 text-primary-color-500 dark:text-primary-color-dark-500"
                          >
                            <path
                              d="M22 19a2 2 0 0 1-2 2H4a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h5l2 3h9a2 2 0 0 1 2 2z"
                            ></path>
                          </svg>
                        </div>
                        <div class="flex flex-row justify-between">
                          <div class="mx-1.5"></div>
                          <div class="mx-1.5">
                            <a
                              href="https://github.com/bachtung2003/glassic-ecommerce"
                              class="text-sm text-white transition duration-200 hover:rotate-180 hover:text-gray-600"
                            >
                              <span class="sr-only">github</span>
                              <svg
                                stroke="#fff"
                                fill="#fff"
                                stroke-width="0"
                                viewBox="0 0 1024 1024"
                                class="text-white hover:text-primary-color-500 dark:text-white dark:hover:text-primary-color-dark-500 h-10 w-10 border rounded-full"
                                height="1em"
                                width="1em"
                                xmlns="http://www.w3.org/2000/svg"
                              >
                                <path
                                  d="M511.6 76.3C264.3 76.2 64 276.4 64 523.5 64 718.9 189.3 885 363.8 946c23.5 5.9 19.9-10.8 19.9-22.2v-77.5c-135.7 15.9-141.2-73.9-150.3-88.9C215 726 171.5 718 184.5 703c30.9-15.9 62.4 4 98.9 57.9 26.4 39.1 77.9 32.5 104 26 5.7-23.5 17.9-44.5 34.7-60.8-140.6-25.2-199.2-111-199.2-213 0-49.5 16.3-95 48.3-131.7-20.4-60.5 1.9-112.3 4.9-120 58.1-5.2 118.5 41.6 123.2 45.3 33-8.9 70.7-13.6 112.9-13.6 42.4 0 80.2 4.9 113.5 13.9 11.3-8.6 67.3-48.8 121.3-43.9 2.9 7.7 24.7 58.3 5.5 118 32.4 36.8 48.9 82.7 48.9 132.3 0 102.2-59 188.1-200 212.9a127.5 127.5 0 0 1 38.1 91v112.5c.8 9 0 17.9 15 17.9 177.1-59.7 304.6-227 304.6-424.1 0-247.2-200.4-447.3-447.5-447.3z"
                                ></path>
                              </svg>
                            </a>
                          </div>
                        </div>
                      </div>
                      <h2
                        class="mb-3 text-2xl font-bold leading-8 tracking-tight"
                      >
                        Glassic
                      </h2>
                      <p
                        class="prose mb-3 max-w-none text-white dark:text-gray-400"
                      >
                        This project involves the development of a modern
                        e-commerce website specializing in glasses using the
                        MERN stack (MongoDB, Express.js, React.js, and Node.js).
                        The website offers a responsive and user-friendly
                        interface for browsing, selecting, and purchasing
                        glasses.
                      </p>
                      <div class="flex flex-row justify-between">
                        <div class="text-sm text-gray-400">MERN stack</div>
                      </div>
                    </div>
                  </div>
                </div>
                <div class="md p-4" style="max-width: 544px">
                  <div
                    class="h-full transform overflow-hidden rounded-md border-2 border-solid border-gray-200 bg-transparent bg-opacity-20 transition duration-500 hover:scale-105 hover:rounded-md hover:border-primary-500 hover:bg-gray-600 dark:border-gray-700 dark:hover:border-primary-500 dark:hover:bg-gray-800"
                  >
                    <div class="p-6">
                      <div class="flex flex-row items-center justify-between">
                        <div class="my-2">
                          <svg
                            xmlns="http://www.w3.org/2000/svg"
                            viewBox="0 0 24 24"
                            fill="#fff"
                            stroke="currentColor"
                            class="h-10 w-10 text-primary-color-500 dark:text-primary-color-dark-500"
                          >
                            <path
                              d="M22 19a2 2 0 0 1-2 2H4a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h5l2 3h9a2 2 0 0 1 2 2z"
                            ></path>
                          </svg>
                        </div>
                        <div class="flex flex-row justify-between">
                          <div class="mx-1.5"></div>
                          <div class="mx-1.5">
                            <a
                              href="https://github.com/bachtung2003/simple-weather-report-website"
                              class="text-sm text-white transition duration-200 hover:rotate-180 hover:text-gray-600"
                            >
                              <span class="sr-only">github</span>
                              <svg
                                stroke="#fff"
                                fill="#fff"
                                stroke-width="0"
                                viewBox="0 0 1024 1024"
                                class="text-white hover:text-primary-color-500 dark:text-white dark:hover:text-primary-color-dark-500 h-10 w-10 border rounded-full"
                                height="1em"
                                width="1em"
                                xmlns="http://www.w3.org/2000/svg"
                              >
                                <path
                                  d="M511.6 76.3C264.3 76.2 64 276.4 64 523.5 64 718.9 189.3 885 363.8 946c23.5 5.9 19.9-10.8 19.9-22.2v-77.5c-135.7 15.9-141.2-73.9-150.3-88.9C215 726 171.5 718 184.5 703c30.9-15.9 62.4 4 98.9 57.9 26.4 39.1 77.9 32.5 104 26 5.7-23.5 17.9-44.5 34.7-60.8-140.6-25.2-199.2-111-199.2-213 0-49.5 16.3-95 48.3-131.7-20.4-60.5 1.9-112.3 4.9-120 58.1-5.2 118.5 41.6 123.2 45.3 33-8.9 70.7-13.6 112.9-13.6 42.4 0 80.2 4.9 113.5 13.9 11.3-8.6 67.3-48.8 121.3-43.9 2.9 7.7 24.7 58.3 5.5 118 32.4 36.8 48.9 82.7 48.9 132.3 0 102.2-59 188.1-200 212.9a127.5 127.5 0 0 1 38.1 91v112.5c.8 9 0 17.9 15 17.9 177.1-59.7 304.6-227 304.6-424.1 0-247.2-200.4-447.3-447.5-447.3z"
                                ></path>
                              </svg>
                            </a>
                          </div>
                        </div>
                      </div>
                      <h2
                        class="mb-3 text-2xl font-bold leading-8 tracking-tight"
                      >
                        Weather Report
                      </h2>
                      <p
                        class="prose mb-3 max-w-none text-white dark:text-gray-400"
                      >
                        A simple web application designed to provide users with
                        real-time weather updates and forecasts in an intuitive
                        and visually appealing manner. Leveraging the power of
                        Vue.js, this project aims to create a seamless and
                        interactive experience for users seeking accurate
                        weather information.
                      </p>
                      <div class="flex flex-row justify-between">
                        <div class="text-sm text-gray-400">Vue.js</div>
                      </div>
                    </div>
                  </div>
                </div>
                <div class="md p-4" style="max-width: 544px">
                  <div
                    class="h-full transform overflow-hidden rounded-md border-2 border-solid border-gray-200 bg-transparent bg-opacity-20 transition duration-500 hover:scale-105 hover:rounded-md hover:border-primary-500 hover:bg-gray-600 dark:border-gray-700 dark:hover:border-primary-500 dark:hover:bg-gray-800"
                  >
                    <div class="p-6">
                      <div class="flex flex-row items-center justify-between">
                        <div class="my-2">
                          <svg
                            xmlns="http://www.w3.org/2000/svg"
                            viewBox="0 0 24 24"
                            fill="#fff"
                            stroke="currentColor"
                            class="h-10 w-10 text-primary-color-500 dark:text-primary-color-dark-500"
                          >
                            <path
                              d="M22 19a2 2 0 0 1-2 2H4a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h5l2 3h9a2 2 0 0 1 2 2z"
                            ></path>
                          </svg>
                        </div>
                        <div class="flex flex-row justify-between">
                          <div class="mx-1.5"></div>
                          <div class="mx-1.5">
                            <a
                              href="https://github.com/bachtung2003/mine-sweeper-game?tab=readme-ov-file"
                              class="text-sm text-white transition duration-200 hover:rotate-180 hover:text-gray-600"
                            >
                              <span class="sr-only">github</span>
                              <svg
                                stroke="#fff"
                                fill="#fff"
                                stroke-width="0"
                                viewBox="0 0 1024 1024"
                                class="text-white hover:text-primary-color-500 dark:text-white dark:hover:text-primary-color-dark-500 h-10 w-10 border rounded-full"
                                height="1em"
                                width="1em"
                                xmlns="http://www.w3.org/2000/svg"
                              >
                                <path
                                  d="M511.6 76.3C264.3 76.2 64 276.4 64 523.5 64 718.9 189.3 885 363.8 946c23.5 5.9 19.9-10.8 19.9-22.2v-77.5c-135.7 15.9-141.2-73.9-150.3-88.9C215 726 171.5 718 184.5 703c30.9-15.9 62.4 4 98.9 57.9 26.4 39.1 77.9 32.5 104 26 5.7-23.5 17.9-44.5 34.7-60.8-140.6-25.2-199.2-111-199.2-213 0-49.5 16.3-95 48.3-131.7-20.4-60.5 1.9-112.3 4.9-120 58.1-5.2 118.5 41.6 123.2 45.3 33-8.9 70.7-13.6 112.9-13.6 42.4 0 80.2 4.9 113.5 13.9 11.3-8.6 67.3-48.8 121.3-43.9 2.9 7.7 24.7 58.3 5.5 118 32.4 36.8 48.9 82.7 48.9 132.3 0 102.2-59 188.1-200 212.9a127.5 127.5 0 0 1 38.1 91v112.5c.8 9 0 17.9 15 17.9 177.1-59.7 304.6-227 304.6-424.1 0-247.2-200.4-447.3-447.5-447.3z"
                                ></path>
                              </svg>
                            </a>
                          </div>
                        </div>
                      </div>
                      <h2
                        class="mb-3 text-2xl font-bold leading-8 tracking-tight"
                      >
                        Minesweeper Game
                      </h2>
                      <p
                        class="prose mb-3 max-w-none text-white dark:text-gray-400"
                      >
                        A simple Minesweeper application in Java involves
                        creating a grid-based game where the user interacts with
                        cells to reveal either numbers (indicating the count of
                        adjacent mines), blank spaces, or mines (leading to a
                        game over).
                      </p>
                      <div class="flex flex-row justify-between">
                        <div class="text-sm text-gray-400">Java</div>
                      </div>
                    </div>
                  </div>
                </div>
                <div class="md p-4" style="max-width: 544px">
                  <div
                    class="h-full transform overflow-hidden rounded-md border-2 border-solid border-gray-200 bg-transparent bg-opacity-20 transition duration-500 hover:scale-105 hover:rounded-md hover:border-primary-500 hover:bg-gray-600 dark:border-gray-700 dark:hover:border-primary-500 dark:hover:bg-gray-800"
                  >
                    <div class="p-6">
                      <div class="flex flex-row items-center justify-between">
                        <div class="my-2">
                          <svg
                            xmlns="http://www.w3.org/2000/svg"
                            viewBox="0 0 24 24"
                            fill="#fff"
                            stroke="currentColor"
                            class="h-10 w-10 text-primary-color-500 dark:text-primary-color-dark-500"
                          >
                            <path
                              d="M22 19a2 2 0 0 1-2 2H4a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h5l2 3h9a2 2 0 0 1 2 2z"
                            ></path>
                          </svg>
                        </div>
                        <div class="flex flex-row justify-between">
                          <div class="mx-1.5"></div>
                          <div class="mx-1.5">
                            <a
                              href="https://github.com/tientrinh2003/CodingGame-OFFICIAL"
                              class="text-sm text-white transition duration-200 hover:rotate-180 hover:text-gray-600"
                            >
                              <span class="sr-only">github</span>
                              <svg
                                stroke="#fff"
                                fill="#fff"
                                stroke-width="0"
                                viewBox="0 0 1024 1024"
                                class="text-white hover:text-primary-color-500 dark:text-white dark:hover:text-primary-color-dark-500 h-10 w-10 border rounded-full"
                                height="1em"
                                width="1em"
                                xmlns="http://www.w3.org/2000/svg"
                              >
                                <path
                                  d="M511.6 76.3C264.3 76.2 64 276.4 64 523.5 64 718.9 189.3 885 363.8 946c23.5 5.9 19.9-10.8 19.9-22.2v-77.5c-135.7 15.9-141.2-73.9-150.3-88.9C215 726 171.5 718 184.5 703c30.9-15.9 62.4 4 98.9 57.9 26.4 39.1 77.9 32.5 104 26 5.7-23.5 17.9-44.5 34.7-60.8-140.6-25.2-199.2-111-199.2-213 0-49.5 16.3-95 48.3-131.7-20.4-60.5 1.9-112.3 4.9-120 58.1-5.2 118.5 41.6 123.2 45.3 33-8.9 70.7-13.6 112.9-13.6 42.4 0 80.2 4.9 113.5 13.9 11.3-8.6 67.3-48.8 121.3-43.9 2.9 7.7 24.7 58.3 5.5 118 32.4 36.8 48.9 82.7 48.9 132.3 0 102.2-59 188.1-200 212.9a127.5 127.5 0 0 1 38.1 91v112.5c.8 9 0 17.9 15 17.9 177.1-59.7 304.6-227 304.6-424.1 0-247.2-200.4-447.3-447.5-447.3z"
                                ></path>
                              </svg>
                            </a>
                          </div>
                        </div>
                      </div>
                      <h2
                        class="mb-3 text-2xl font-bold leading-8 tracking-tight"
                      >
                        2D RPG Game
                      </h2>
                      <p
                        class="prose mb-3 max-w-none text-white dark:text-gray-400"
                      >
                        A 2D pixel-based role-playing game that invites players
                        to dive into a mythical world full of mystery,
                        adventure, and challenge. Designed with inspiration from
                        the Quest Hunter series, the game emphasizes monster
                        battles and treasure hunts as core gameplay elements. It
                        aims to deliver an engaging and immersive experience for
                        players who enjoy exploration, strategy, and discovery.
                      </p>
                      <div class="flex flex-row justify-between">
                        <div class="text-sm text-gray-400">Java</div>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div id="footer">
        <footer class="relative">
          <div
            class="block w-full pt-[10vh] max-2xl:bg-white"
            :style="
              isMax2xl
                ? null
                : { backgroundColor: scaleY2 === 1 ? '#000' : '#fff' }
            "
          >
            <div class="flex flex-row px-24 pb-24 max-md:flex-col">
              <div class="mr-32 flex-1 flex flex-col justify-between">
                <div></div>
                <div class="uppercase">
                  <div class="mb-14">
                    <h3 class="w-max">QUICK LINKS</h3>
                    <ul class="flex items-center">
                      <li class="mr-4">
                        <a href="" class="underline cursor-pointer text-black">
                          <span>Home</span></a
                        >
                      </li>
                      <li class="mr-4">—</li>
                      <li class="mr-4">
                        <a href="" class="underline cursor-pointer text-black">
                          <span>Project</span></a
                        >
                      </li>
                      <!-- <li class="mr-4">—</li>
                      <li class="mr-4">
                        <a href="" class="underline cursor-pointer text-black">
                          <span>Home</span></a
                        >
                      </li> -->
                    </ul>
                  </div>
                  <div class="mb-14">
                    <h3>EXTRAS</h3>
                    <ul class="flex items-center">
                      <li class="mr-4">
                        <a href="https://drive.google.com/file/d/1JF7t-I1rf80goyHMcjbaKIG3ruTIqzDJ/view?usp=sharing" class="underline cursor-pointer text-black">
                          <span>Resume</span></a
                        >
                      </li>
                      <!-- <li class="mr-4">—</li>
                      <li class="mr-4">
                        <a href="" class="underline cursor-pointer text-black">
                          <span>Home</span></a
                        >
                      </li>
                      <li class="mr-4">—</li>
                      <li class="mr-4">
                        <a href="" class="underline cursor-pointer text-black">
                          <span>Home</span></a
                        >
                      </li> -->
                    </ul>
                  </div>
                </div>
              </div>
              <div class="flex-1">
                <section>
                  <h4 class="text-6xl max-lg:text-4xl font-bold">
                    Would love to hear <br />
                    from you ↓.
                  </h4>
                  <p class="mt-6">
                    If you have requests or questions, kindly do not hesitate to
                    contact me.
                  </p>
                </section>
                <a
                  class="text-5xl max-lg:text-3xl max-sm:text-base"
                  target="_blank"
                  data-link="email"
                  style="text-transform: lowercase"
                  href="mailto: tranbachtungnvc@gmail.com"
                  ><span class="underline">tranbachtungnvc@gmail.com</span></a
                >
              </div>
            </div>
            <div
              class="mx-24 flex flex-row p-0 border-t items-center py-4 justify-between max-sm:flex-col max-sm:gap-4"
            >
              <ul class="flex flex-row gap-4 max-sm:flex-col">
                <li>
                  <a
                    target="_blank"
                    data-link="linkedin"
                    href="https://www.linkedin.com/in/tung-tran-83137212b/"
                    ><span>Linkedin</span></a
                  >
                </li>
                <li>
                  <a
                    target="_blank"
                    data-link="github"
                    href="https://github.com/bachtung2003"
                    ><span>Github</span></a
                  >
                </li>
                <li>
                  <a
                    target="_blank"
                    data-link="facebook"
                    href="https://www.facebook.com/tran.bach.tung.6034/"
                    ><span>Facebook</span></a
                  >
                </li>
                <li>
                  <a
                    target="_blank"
                    data-link="instagram"
                    href="https://www.instagram.com/lilt.20_04.mob/"
                    ><span>Instagram</span></a
                  >
                </li>
              </ul>
              <p>© 2024 Tran Bach Tung</p>
            </div>
          </div>
          <div
            class="max-lg:hidden w-full fixed left-0 bottom-0 bg-black will-change-transform"
            :style="{
              transformOrigin: 'bottom',
              height: '50vh',
              transform: `translate3d(0px, 0px, 0px) scale(1, ${scaleY2})`,
              zIndex: scaleY2 == 1 ? -1 : 2,
            }"
          ></div>
        </footer>
      </div>
    </div>
  </div>
</template>

<style>
/* Slide-in animation */
@keyframes slide-in-left {
  0% {
    transform: translateX(-100%);
  }
  100% {
    transform: translateX(0);
  }
}
@keyframes slide-in-right {
  0% {
    transform: translateX(100%);
  }
  100% {
    transform: translateX(0);
  }
}
@keyframes slide-down {
  0% {
    transform: translateY(-100%);
    opacity: 0;
  }
  100% {
    transform: translateY(0);
    opacity: 1;
  }
}
@keyframes autoRotateAnimation {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}
.autoRotate {
  animation: autoRotateAnimation 5s linear infinite;
}

/* Add a delay to the slide-in animations */
.animate-slide-in-delay-left {
  animation: slide-in-left 2s ease-out forwards;
}
.animate-slide-in-delay-right {
  animation: slide-in-right 2s ease-out forwards;
}
/* Slide-down animation */
.animate-slide-down {
  animation: slide-down 2s ease-out forwards;
}
span {
  display: inline-block !important;
  transition: opacity 0.2s ease-in-out; /* Smooth transition for opacity */
}
#skill-image {
  transition: transform 0.5s ease-out; /* Smooth transition */
}
#skill-span {
  transition: transform 0.2s ease-out; /* Smooth transition for the transform property */
  display: inline-block; /* Ensure inline span can be transformed */
}
.slider {
  width: 100%;
  height: var(--height);
  overflow: hidden;
  mask-image: linear-gradient(to right, transparent, #000 10% 90%, transparent);
}
.slider .list {
  display: flex;
  width: 100%;
  min-width: calc(var(--width) * var(--quantity));
  position: relative;
}
.slider .list .item {
  width: var(--width);
  height: var(--height);
  position: absolute;
  left: 100%;
  animation: autoRun 10s linear infinite;
  transition: filter 0.5s;
  animation-delay: calc(
    (10s / var(--quantity)) * (var(--position) - 1) - 10s
  ) !important;
}
.slider .list .item img {
  border-radius: 5px;
  object-fit: scale-down;
  background-color: #fff;
  width: 100%;
  height: 100%;
}
@keyframes autoRun {
  from {
    left: 100%;
  }
  to {
    left: calc(var(--width) * -1);
  }
}
.slider:hover .item {
  animation-play-state: paused !important;
  filter: grayscale(1);
}
.slider .item:hover {
  filter: grayscale(0);
}
.slider[reverse="true"] .item {
  animation: reversePlay 10s linear infinite;
}
@keyframes reversePlay {
  from {
    left: calc(var(--width) * -1);
  }
  to {
    left: 100%;
  }
}
</style>
