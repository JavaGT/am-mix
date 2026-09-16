<script setup lang="ts">
import { ref, watch } from 'vue';
import { useRouter } from 'vue-router';
import { useDownloadManager } from '../composables/useDownloadManager';

const router = useRouter();

const { addToQueue, lastQueueError } = useDownloadManager();

const query = ref('');

// Hosts the queue error snackbar: this bar is always mounted once the app is
// initialized, which is the only state in which addToQueue can fail.
const isQueueErrorVisible = ref(false);
const queueErrorMessage = ref('');

watch(lastQueueError, (error) => {
  if (!error) return;

  queueErrorMessage.value = error.message;
  isQueueErrorVisible.value = true;
});

const handleSearch = () => {
  const queryTrimmed = query.value.trim();
  if (queryTrimmed.startsWith('https://music.apple.com')) {
    addToQueue(queryTrimmed);
    query.value = '';
  } else if (queryTrimmed) {
    router.push(`/search/${encodeURIComponent(queryTrimmed)}`);
  }
};
</script>

<template>
  <v-text-field v-model="query" rounded :placeholder="$t('searchBar.placeholder')" hide-details variant="outlined"
    density="compact" @keyup.enter="handleSearch" />

  <v-snackbar v-model="isQueueErrorVisible" color="error" :timeout="5000" data-testid="queue-error-snackbar">
    {{ queueErrorMessage }}
  </v-snackbar>
</template>
