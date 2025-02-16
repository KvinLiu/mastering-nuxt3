<template>
  <div>
    <p class="mt-0 mb-1 font-bold uppercase text-slate-400">
      Lesson {{ chapter.number }} - {{ lesson.number }}
    </p>
    <h2 class="my-0">{{ lesson.title }}</h2>
    <div class="flex mt-2 mb-8 space-x-4">
      <NuxtLink
        v-if="lesson.sourceUrl"
        class="font-normal text-gray-500 text-md"
        :to="lesson.sourceUrl"
      >
        Download Source Code
      </NuxtLink>
      <NuxtLink
        v-if="lesson.downloadUrl"
        class="font-normal text-gray-500 text-md"
        :to="lesson.downloadUrl"
      >
        Download Video
      </NuxtLink>
    </div>
    <VideoPlayer v-if="lesson.videoId" :videoId="lesson.videoId" />
    <p>{{ lesson.text }}</p>
    <LessonCompleteButton
      :modelValue="isLessonComplete"
      @update:modelValue="toggleComplete"
    />
  </div>
</template>

<script setup>
const course = useCourse();
const route = useRoute();

definePageMeta({
  middleware: [
    function ({ params }, from) {
      const course = useCourse();
      const chapter = course.chapters.find(
        (chapter) => chapter.slug === params.chapterSlug,
      );
      if (!chapter) {
        return abortNavigation(
          createError({
            statusCode: 404,
            message: "Chapter not found",
          }),
        );
      }
      const lesson = chapter.lessons.find(
        (lesson) => lesson.slug === params.lessonSlug,
      );
      if (!lesson) {
        return abortNavigation(
          createError({
            statusCode: 404,
            message: "Lesson not found",
          }),
        );
      }
    },
    function (to, from) {
      if (to.params.chapterSlug === "1-chapter-1") {
        return;
      }
      return navigateTo("/login");
    },
  ],
});

if (route.params.lessonSlug === "3-typing-component-events") {
  console.log(
    route.params.paramthatdoesnotexistwhoops.capitalizeIsNotAMethod(),
  );
}

const chapter = computed(() => {
  return course.chapters.find(
    (chapter) => chapter.slug === route.params.chapterSlug,
  );
});

const lesson = computed(() => {
  return chapter.value.lessons.find(
    (lesson) => lesson.slug === route.params.lessonSlug,
  );
});

const title = computed(() => {
  return `${lesson.value.title} | ${chapter.value.title} | Mastering Nuxt 3`;
});

useHead({
  title,
});

const progress = useLocalStorage("progress", []);
const chpNumber = computed(() => {
  return chapter.value.number - 1;
});
const lesNumber = computed(() => {
  return lesson.value.number - 1;
});
const isLessonComplete = computed(() => {
  if (!progress.value[chpNumber.value]) {
    return false;
  }
  if (!progress.value[chpNumber.value][lesNumber.value]) {
    return false;
  }
  return progress.value[chpNumber.value][lesNumber.value];
});
const toggleComplete = () => {
  throw createError("Could not update");
  if (!progress.value[chpNumber.value]) {
    progress.value[chpNumber.value] = [];
  }
  progress.value[chpNumber.value][lesNumber.value] = !isLessonComplete.value;
};
</script>
