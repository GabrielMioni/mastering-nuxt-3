<template>
  <div>
    <p class="mt-0 uppercase font-bold text-slate-400 mb-1">
      Lesson {{ chapter.number }} - {{ lesson.number }}
    </p>
    <h2 class="my-0">{{ lesson.title }}</h2>
    <div class="flex space-x-4 mt-2 mb-8">
      <a
        v-if="lesson.sourceUrl"
        class="font-normal text-md text-gray-500"
        :href="lesson.sourceUrl"
      >
        Download Source Code
      </a>
      <video-player :video-id="lesson.videoId"/>
    </div>
    <p>{{ lesson.text }}</p>
    <LessonCompleteButton v-model="isComplete"/>
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
        (chapter) => chapter.slug === params.chapterSlug
      );

      if (!chapter) {
        return abortNavigation(
          createError({
            statusCode: 404,
            message: 'Chapter not found'
          })
        )
      }

      const lesson = chapter.lessons.find(
        (lesson) => lesson.slug === params.lessonSlug
      );

      if (!lesson) {
        return abortNavigation(
          createError({
            statusCode: 404,
            message: 'Lesson not found'
          })
        )
      }
    },
    'auth'
  ],
})

const chapter = computed(() => {
  return course.chapters.find(
    (chapter) => chapter.slug === route.params.chapterSlug
  );
});

const lesson = computed(() => {
  return chapter.value.lessons.find(
    (lesson) => lesson.slug === route.params.lessonSlug
  );
});

const title = computed(() => `${lesson.value.title} - ${course.title}`);

useHead({
  // title: `${lesson.value.title} - Mastering Nuxt 3`,
  title: title.value,
  meta: [
    {
      name: 'description',
      content: lesson.value.text,
    },
  ],
});

const progress = useLocalStorage('progress', () => {
  return []
})

const isLessonComplete = computed(() => {
  if (!progress.value[chapter.value.number - 1]) {
    return false
  }

  if (!progress.value[chapter.value.number - 1][lesson.value.number - 1]) {
    return false
  }

  return progress.value[chapter.value.number - 1][lesson.value.number - 1]
})

import { computed } from 'vue';

const isComplete = computed({
  get () {
    return isLessonComplete.value
  },
  set (value) {
    // throw error('Stuff is broken')
    // Update the progress state based on the new value
    if (chapter.value && lesson.value) {
      if (!progress.value[chapter.value.number - 1]) {
        progress.value[chapter.value.number - 1] = [];
      }
      progress.value[chapter.value.number - 1][lesson.value.number - 1] = value;
    }
  }
});



</script>
