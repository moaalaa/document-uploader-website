<template>
  <div class="flex justify-center items-center min-h-[calc(100vh-200px)]">
    <div class="card w-full max-w-2xl bg-base-100 shadow-2xl">
      <div class="card-body">
        <h2 class="card-title text-3xl font-bold mb-6 justify-center">
          <svg xmlns="http://www.w3.org/2000/svg" class="h-8 w-8 mr-2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M15 13l-3-3m0 0l-3 3m3-3v12" />
          </svg>
          Upload Video
        </h2>

        <!-- File Input -->
        <div class="form-control w-full mb-4">
          <label class="label">
            <span class="label-text font-semibold">Select video file</span>
            <span class="label-text-alt">Max size: 2GB</span>
          </label>
          <input 
            type="file" 
            class="file-input file-input-bordered file-input-primary w-full" 
            accept="video/*"
            @change="handleFileSelect"
            :disabled="isUploading"
          />
        </div>

        <!-- Selected File Info -->
        <div v-if="selectedFile" class="alert alert-info mb-4">
          <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" class="stroke-current shrink-0 w-6 h-6">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"></path>
          </svg>
          <div>
            <div class="font-bold">{{ selectedFile.name }}</div>
            <div class="text-xs">{{ formatFileSize(selectedFile.size) }}</div>
          </div>
        </div>

        <!-- Progress Bar -->
        <div v-if="isUploading || uploadProgress > 0" class="mb-4">
          <div class="flex justify-between mb-2">
            <span class="text-sm font-semibold">Upload Progress</span>
            <span class="text-sm font-semibold">{{ uploadProgress }}%</span>
          </div>
          <progress 
            class="progress progress-primary w-full" 
            :value="uploadProgress" 
            max="100"
          ></progress>
        </div>

        <!-- Status Alert -->
        <div v-if="uploadStatus" class="alert mb-4" :class="getAlertClass()">
          <svg v-if="uploadStatus === 'uploading'" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" class="stroke-current shrink-0 w-6 h-6 animate-spin">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15" />
          </svg>
          <svg v-else-if="uploadStatus === 'processing'" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" class="stroke-current shrink-0 w-6 h-6">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"></path>
          </svg>
          <svg v-else-if="uploadStatus === 'completed'" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" class="stroke-current shrink-0 w-6 h-6">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" />
          </svg>
          <span>{{ getStatusMessage() }}</span>
        </div>

        <!-- Action Buttons -->
        <div class="card-actions justify-end mt-4">
          <button 
            class="btn btn-outline btn-error" 
            @click="cancelUpload"
            :disabled="!isUploading && !selectedFile"
          >
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
            </svg>
            Cancel
          </button>
          <button 
            class="btn btn-primary" 
            @click="startUpload"
            :disabled="!selectedFile || isUploading"
          >
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mr-2" fill="none" viewBox="0 0 24 24" stroke="currentColor">
              <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M7 16a4 4 0 01-.88-7.903A5 5 0 1115.9 6L16 6a5 5 0 011 9.9M15 13l-3-3m0 0l-3 3m3-3v12" />
            </svg>
            Upload
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import api from '@/services/api'
import { formatFileSize } from '@/utils/formatters'

const router = useRouter()
const selectedFile = ref(null)
const isUploading = ref(false)
const uploadProgress = ref(0)
const uploadStatus = ref(null) // 'uploading', 'processing', 'completed', 'error'
const errorMessage = ref('')

const handleFileSelect = (event) => {
  const file = event.target.files[0]
  if (file) {
    selectedFile.value = file
    uploadProgress.value = 0
    uploadStatus.value = null
    errorMessage.value = ''
  }
}

const getVideoMetadata = (file) => {
  return new Promise((resolve) => {
    const video = document.createElement('video')
    video.preload = 'metadata'
    video.onloadedmetadata = () => {
      window.URL.revokeObjectURL(video.src)
      const duration = Math.round(video.duration)
      const minutes = Math.floor(duration / 60)
      const seconds = duration % 60
      const formattedDuration = `${minutes}:${seconds.toString().padStart(2, '0')}`

      resolve({
        duration: formattedDuration,
        width: video.videoWidth,
        height: video.videoHeight,
        resolution: `${video.videoWidth}x${video.videoHeight}`
      })
    }
    video.src = URL.createObjectURL(file)
  })
}

const startUpload = async () => {
  if (!selectedFile.value) return
  
  isUploading.value = true
  uploadStatus.value = 'uploading'
  uploadProgress.value = 0
  errorMessage.value = ''

  try {
    // Extract metadata
    const metadata = await getVideoMetadata(selectedFile.value)

    const formData = new FormData()
    formData.append('video', selectedFile.value)
    formData.append('title', selectedFile.value.name.split('.').slice(0, -1).join('.'))
    formData.append('file_size', selectedFile.value.size) // raw bytes
    formData.append('duration', metadata.duration)
    formData.append('resolution', metadata.resolution)
    formData.append('format', selectedFile.value.type.split('/')[1]?.toUpperCase() || 'MP4')

    const response = await api.post('/videos', formData, {
      headers: {
        'Content-Type': 'multipart/form-data'
      },
      onUploadProgress: (progressEvent) => {
        const percentCompleted = Math.round((progressEvent.loaded * 100) / progressEvent.total)
        uploadProgress.value = percentCompleted

        if (percentCompleted === 100) {
          uploadStatus.value = 'processing'
        }
      }
    })

    uploadStatus.value = 'completed'
    isUploading.value = false

    // Redirect to My Videos after 2 seconds
    setTimeout(() => {
      router.push('/videos')
    }, 2000)
  } catch (err) {
    console.error('Upload failed:', err)
    uploadStatus.value = 'error'
    isUploading.value = false
    errorMessage.value = err.response?.data?.message || 'Failed to upload video. Please check your connection and try again.'
  }
}

const cancelUpload = () => {
  selectedFile.value = null
  isUploading.value = false
  uploadProgress.value = 0
  uploadStatus.value = null
  errorMessage.value = ''
}

const getAlertClass = () => {
  switch (uploadStatus.value) {
    case 'uploading':
      return 'alert-info'
    case 'processing':
      return 'alert-warning'
    case 'completed':
      return 'alert-success'
    case 'error':
      return 'alert-error'
    default:
      return ''
  }
}

const getStatusMessage = () => {
  switch (uploadStatus.value) {
    case 'uploading':
      return 'Uploading your video...'
    case 'processing':
      return 'Processing video...'
    case 'completed':
      return 'Upload completed! Redirecting to My Videos...'
    case 'error':
      return errorMessage.value
    default:
      return ''
  }
}
</script>
