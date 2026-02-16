<template>
  <div>
    <!-- Back Button -->
    <div class="mb-6">
      <button @click="goBack" class="btn btn-ghost btn-sm">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 19l-7-7m0 0l7-7m-7 7h18" />
        </svg>
        Back to My Videos
      </button>
    </div>

    <!-- Loading State -->
    <div v-if="loading" class="flex flex-col items-center justify-center min-h-[400px]">
      <span class="loading loading-spinner loading-lg text-primary"></span>
      <p class="mt-4 text-base-content/70">Loading video details...</p>
    </div>

    <!-- Error State -->
    <div v-else-if="error" class="alert alert-error shadow-lg max-w-2xl mx-auto">
      <svg xmlns="http://www.w3.org/2000/svg" class="stroke-current shrink-0 h-6 w-6" fill="none" viewBox="0 0 24 24">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2"
          d="M10 14l2-2m0 0l2-2m-2 2l-2-2m2 2l2 2m7-2a9 9 0 11-18 0 9 9 0 0118 0z" />
      </svg>
      <span>{{ error }}</span>
      <button @click="goBack" class="btn btn-sm btn-ghost">Go Back</button>
    </div>

    <div v-else-if="video" class="max-w-5xl mx-auto animate-in fade-in duration-500">
      <!-- Video Player -->
      <div class="card bg-base-100 shadow-2xl mb-6">
        <figure class="bg-black">
          <video 
            ref="videoPlayer"
            class="w-full max-h-[600px]" 
            controls
            :poster="video.thumbnail"
          >
            <source :src="video.video_url" type="video/mp4">
            Your browser does not support the video tag.
          </video>
        </figure>
      </div>

      <div class="divider"></div>

      <!-- Video Details Card -->
      <div class="card bg-base-100 shadow-xl">
        <div class="card-body">
          <div class="flex justify-between items-start mb-4">
            <div>
              <h1 class="card-title text-3xl font-bold mb-2">{{ video.title }}</h1>
              <div class="flex gap-2 items-center">
                <div class="badge badge-lg" :class="getBadgeClass(video.status)">
                  {{ video.status }}
                </div>
                <div v-if="video.isNew" class="badge badge-secondary badge-lg">NEW</div>
              </div>
            </div>
            <button 
              @click="showDeleteModal = true" 
              class="btn btn-error btn-outline"
            >
              <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
              </svg>
              Delete
            </button>
          </div>

          <div class="divider"></div>

          <!-- Video Information -->
          <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
            <div class="space-y-4">
              <div class="flex items-center">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-3 text-primary" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 21h10a2 2 0 002-2V9.414a1 1 0 00-.293-.707l-5.414-5.414A1 1 0 0012.586 3H7a2 2 0 00-2 2v14a2 2 0 002 2z" />
                </svg>
                <div>
                  <p class="text-sm text-base-content/70">File Size</p>
                  <p class="font-semibold">{{ formatFileSize(video.fileSize || video.size || video.file_size) }}</p>
                </div>
              </div>

              <div class="flex items-center">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-3 text-primary" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z" />
                </svg>
                <div>
                  <p class="text-sm text-base-content/70">Duration</p>
                  <p class="font-semibold">{{ video.duration }}</p>
                </div>
              </div>

              <div class="flex items-center">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-3 text-primary" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 10l4.553-2.276A1 1 0 0121 8.618v6.764a1 1 0 01-1.447.894L15 14M5 18h8a2 2 0 002-2V8a2 2 0 00-2-2H5a2 2 0 00-2 2v8a2 2 0 002 2z" />
                </svg>
                <div>
                  <p class="text-sm text-base-content/70">Format</p>
                  <p class="font-semibold">{{ video.format }}</p>
                </div>
              </div>
            </div>

            <div class="space-y-4">
              <div class="flex items-center">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-3 text-primary" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 7V3m8 4V3m-9 8h10M5 21h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v12a2 2 0 002 2z" />
                </svg>
                <div>
                  <p class="text-sm text-base-content/70">Upload Date</p>
                  <p class="font-semibold">{{ formatDate(video.created_at) }}</p>
                </div>
              </div>

              <div class="flex items-center">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-3 text-primary" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" />
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M2.458 12C3.732 7.943 7.523 5 12 5c4.478 0 8.268 2.943 9.542 7-1.274 4.057-5.064 7-9.542 7-4.477 0-8.268-2.943-9.542-7z" />
                </svg>
                <div>
                  <p class="text-sm text-base-content/70">Views</p>
                  <p class="font-semibold">{{ video.views }}</p>
                </div>
              </div>

              <div class="flex items-center">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-3 text-primary" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z" />
                </svg>
                <div>
                  <p class="text-sm text-base-content/70">Resolution</p>
                  <p class="font-semibold">{{ video.resolution }}</p>
                </div>
              </div>
            </div>
          </div>

          <div class="divider"></div>

          <!-- Description -->
          <div v-if="video.description">
            <h3 class="font-bold text-lg mb-2">Description</h3>
            <p class="text-base-content/80">{{ video.description }}</p>
          </div>
        </div>
      </div>
    </div>

    <!-- Delete Confirmation Modal -->
    <dialog :class="{ 'modal': true, 'modal-open': showDeleteModal }">
      <div class="modal-box">
        <h3 class="font-bold text-lg mb-4">Delete Video</h3>
        <p class="py-4">Are you sure you want to delete "{{ video?.title }}"? This action cannot be undone.</p>
        <div class="modal-action">
          <button @click="showDeleteModal = false" class="btn btn-ghost">Cancel</button>
          <button @click="deleteVideo" class="btn btn-error" :disabled="isDeleting">
            <span v-if="isDeleting" class="loading loading-spinner loading-xs"></span>
            <svg v-else xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" fill="none" viewBox="0 0 24 24"
              stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
            </svg>
            {{ isDeleting ? 'Deleting...' : 'Delete' }}
          </button>
        </div>
      </div>
      <form method="dialog" class="modal-backdrop">
        <button @click="showDeleteModal = false">close</button>
      </form>
    </dialog>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import api from '@/services/api'
import { formatFileSize, formatDate } from '@/utils/formatters'

const route = useRoute()
const router = useRouter()
const videoPlayer = ref(null)
const showDeleteModal = ref(false)
const video = ref(null)
const loading = ref(true)
const error = ref(null)
const isDeleting = ref(false)

const fetchVideo = async () => {
  loading.value = true
  error.value = null
  try {
    const videoId = route.params.id
    const response = await api.get(`/videos/${videoId}`)
    video.value = response.data.data || response.data
  } catch (err) {
    console.error('Error fetching video:', err)
    error.value = 'Failed to load video details. It might have been deleted or is currently unavailable.'
  } finally {
    loading.value = false
  }
}

onMounted(() => {
  fetchVideo()
})

const getBadgeClass = (status) => {
  switch (status) {
    case 'completed':
      return 'badge-success'
    case 'processing':
      return 'badge-warning'
    case 'uploading':
      return 'badge-info'
    default:
      return 'badge-ghost'
  }
}

const goBack = () => {
  router.push('/videos')
}

const deleteVideo = async () => {
  if (!video.value) return

  isDeleting.value = true
  try {
    await api.delete(`/videos/${video.value.id}`)
    showDeleteModal.value = false
    router.push('/videos')
  } catch (err) {
    console.error('Error deleting video:', err)
    alert('Failed to delete video. Please try again.')
  } finally {
    isDeleting.value = false
  }
}
</script>
