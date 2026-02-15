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

    <div v-if="video" class="max-w-5xl mx-auto">
      <!-- Video Player -->
      <div class="card bg-base-100 shadow-2xl mb-6">
        <figure class="bg-black">
          <video 
            ref="videoPlayer"
            class="w-full max-h-[600px]" 
            controls
            :poster="video.thumbnail"
          >
            <source :src="video.videoUrl" type="video/mp4">
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
                  <p class="font-semibold">{{ video.fileSize }}</p>
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
                  <p class="font-semibold">{{ video.uploadDate }}</p>
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
          <button @click="deleteVideo" class="btn btn-error">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" />
            </svg>
            Delete
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

const route = useRoute()
const router = useRouter()
const videoPlayer = ref(null)
const showDeleteModal = ref(false)

// Sample video data (in a real app, this would come from an API)
const video = ref(null)

const videoData = {
  1: {
    id: 1,
    title: 'Product Demo Video',
    thumbnail: 'https://images.unsplash.com/photo-1492619375914-88005aa9e8fb?w=800&h=600&fit=crop',
    videoUrl: 'https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/BigBuckBunny.mp4',
    fileSize: '245 MB',
    duration: '5:32',
    status: 'completed',
    isNew: true,
    format: 'MP4',
    uploadDate: 'Feb 15, 2026',
    views: '1,234',
    resolution: '1920x1080',
    description: 'This is a comprehensive product demonstration showcasing all the key features and benefits of our latest offering. Perfect for sales presentations and customer onboarding.'
  },
  2: {
    id: 2,
    title: 'Team Meeting Recording',
    thumbnail: 'https://images.unsplash.com/photo-1611162617474-5b21e879e113?w=800&h=600&fit=crop',
    videoUrl: 'https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/ElephantsDream.mp4',
    fileSize: '512 MB',
    duration: '12:45',
    status: 'completed',
    isNew: false,
    format: 'MP4',
    uploadDate: 'Feb 10, 2026',
    views: '856',
    resolution: '1920x1080',
    description: 'Weekly team sync meeting covering project updates, roadmap planning, and Q&A session.'
  },
  3: {
    id: 3,
    title: 'Tutorial Series - Part 1',
    thumbnail: 'https://images.unsplash.com/photo-1516321497487-e288fb19713f?w=800&h=600&fit=crop',
    videoUrl: 'https://commondatastorage.googleapis.com/gtv-videos-bucket/sample/ForBiggerBlazes.mp4',
    fileSize: '189 MB',
    duration: '8:15',
    status: 'processing',
    isNew: false,
    format: 'MP4',
    uploadDate: 'Feb 14, 2026',
    views: '2,145',
    resolution: '1280x720',
    description: 'First episode in our comprehensive tutorial series for beginners.'
  }
}

onMounted(() => {
  const videoId = parseInt(route.params.id)
  video.value = videoData[videoId] || videoData[1]
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

const deleteVideo = () => {
  showDeleteModal.value = false
  // In a real app, you would make an API call here
  setTimeout(() => {
    router.push('/videos')
  }, 500)
}
</script>
