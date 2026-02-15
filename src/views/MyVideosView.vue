<template>
  <div>
    <div class="flex justify-between items-center mb-8">
      <h1 class="text-4xl font-bold">My Videos</h1>
      <RouterLink to="/upload" class="btn btn-primary">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4v16m8-8H4" />
        </svg>
        Upload New Video
      </RouterLink>
    </div>

    <!-- Videos Grid -->
    <div v-if="videos.length > 0" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
      <div 
        v-for="video in videos" 
        :key="video.id" 
        class="card bg-base-100 shadow-xl hover:shadow-2xl transition-shadow duration-300"
      >
        <figure class="relative h-48 bg-base-300">
          <img 
            :src="video.thumbnail" 
            :alt="video.title"
            class="w-full h-full object-cover"
          />
          <div class="absolute top-2 right-2">
            <div class="badge badge-lg" :class="getBadgeClass(video.status)">
              {{ video.status }}
            </div>
          </div>
          <div class="absolute inset-0 flex items-center justify-center opacity-0 hover:opacity-100 bg-black bg-opacity-50 transition-opacity duration-300">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-16 w-16 text-white" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M14.752 11.168l-3.197-2.132A1 1 0 0010 9.87v4.263a1 1 0 001.555.832l3.197-2.132a1 1 0 000-1.664z" />
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
            </svg>
          </div>
        </figure>
        <div class="card-body">
          <h2 class="card-title">
            {{ video.title }}
            <div v-if="video.isNew" class="badge badge-secondary">NEW</div>
          </h2>
          <p class="text-sm text-base-content/70">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 inline mr-1" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 21h10a2 2 0 002-2V9.414a1 1 0 00-.293-.707l-5.414-5.414A1 1 0 0012.586 3H7a2 2 0 00-2 2v14a2 2 0 002 2z" />
            </svg>
            {{ video.fileSize }}
          </p>
          <p class="text-xs text-base-content/50">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-3 w-3 inline mr-1" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8v4l3 3m6-3a9 9 0 11-18 0 9 9 0 0118 0z" />
            </svg>
            {{ video.duration }}
          </p>
          <div class="card-actions justify-end mt-4">
            <button 
              @click="viewVideo(video.id)" 
              class="btn btn-primary btn-sm"
            >
              <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 mr-1" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" />
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M2.458 12C3.732 7.943 7.523 5 12 5c4.478 0 8.268 2.943 9.542 7-1.274 4.057-5.064 7-9.542 7-4.477 0-8.268-2.943-9.542-7z" />
              </svg>
              View
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- Empty State -->
    <div v-else class="flex flex-col items-center justify-center min-h-[400px] text-center">
      <svg xmlns="http://www.w3.org/2000/svg" class="h-24 w-24 text-base-content/30 mb-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 10l4.553-2.276A1 1 0 0121 8.618v6.764a1 1 0 01-1.447.894L15 14M5 18h8a2 2 0 002-2V8a2 2 0 00-2-2H5a2 2 0 00-2 2v8a2 2 0 002 2z" />
      </svg>
      <h3 class="text-2xl font-bold mb-2">No videos yet</h3>
      <p class="text-base-content/70 mb-6">Upload your first video to get started</p>
      <RouterLink to="/upload" class="btn btn-primary">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M15 13l-3-3m0 0l-3 3m3-3v12" />
        </svg>
        Upload Video
      </RouterLink>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter, RouterLink } from 'vue-router'

const router = useRouter()

// Sample video data
const videos = ref([
  {
    id: 1,
    title: 'Product Demo Video',
    thumbnail: 'https://images.unsplash.com/photo-1492619375914-88005aa9e8fb?w=400&h=300&fit=crop',
    fileSize: '245 MB',
    duration: '5:32',
    status: 'completed',
    isNew: true
  },
  {
    id: 2,
    title: 'Team Meeting Recording',
    thumbnail: 'https://images.unsplash.com/photo-1611162617474-5b21e879e113?w=400&h=300&fit=crop',
    fileSize: '512 MB',
    duration: '12:45',
    status: 'completed',
    isNew: false
  },
  {
    id: 3,
    title: 'Tutorial Series - Part 1',
    thumbnail: 'https://images.unsplash.com/photo-1516321497487-e288fb19713f?w=400&h=300&fit=crop',
    fileSize: '189 MB',
    duration: '8:15',
    status: 'processing',
    isNew: false
  },
  {
    id: 4,
    title: 'Customer Testimonial',
    thumbnail: 'https://images.unsplash.com/photo-1574717024653-61fd2cf4d44d?w=400&h=300&fit=crop',
    fileSize: '98 MB',
    duration: '3:22',
    status: 'completed',
    isNew: false
  },
  {
    id: 5,
    title: 'Marketing Campaign Video',
    thumbnail: 'https://images.unsplash.com/photo-1536240478700-b869070f9279?w=400&h=300&fit=crop',
    fileSize: '356 MB',
    duration: '6:48',
    status: 'uploading',
    isNew: true
  },
  {
    id: 6,
    title: 'Webinar Recording',
    thumbnail: 'https://images.unsplash.com/photo-1587825140708-dfaf72ae4b04?w=400&h=300&fit=crop',
    fileSize: '678 MB',
    duration: '45:12',
    status: 'completed',
    isNew: false
  }
])

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

const viewVideo = (id) => {
  router.push(`/video/${id}`)
}
</script>
