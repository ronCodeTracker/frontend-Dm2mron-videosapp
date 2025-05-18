<script>
  import { onMount } from 'svelte';
  let videos = [];
  let name = '';
  let category = 'search_and_rescue';
  let video;

  let selectedVideo = null;
  let loadingVideo = false;

  const API_BASE_URL = 'https://allenskywolf.com';

  async function fetchVideos() {
    try {
      const res = await fetch('https://allenskywolf.com/getAllVideos');
      if (!res.ok) throw new Error('Failed to fetch videos');
      videos = await res.json();
    } catch (error) {
      console.error('Error fetching videos:', error);
    }
  }

  async function uploadVideo() {
    const CHUNK_SIZE = 5 * 1024 * 1024;
    const totalChunks = Math.ceil(video.size / CHUNK_SIZE);

    loadingVideo = true;console.log("upload: " + loadingVideo); )

    for (let i = 0; i < totalChunks; i++) {
      const chunk = video.slice(i * CHUNK_SIZE, (i + 1) * CHUNK_SIZE);
      const formData = new FormData();
      formData.append('name', name);
      formData.append('category', category);
      formData.append('chunkIndex', i);
      formData.append('totalChunks', totalChunks);
      formData.append('video', chunk);

      try {
        const res = await fetch(`${API_BASE_URL}/createVideo`, {
          method: 'POST',
          body: formData,
        });
        if (!res.ok) throw new Error('Failed to upload chunk');
      } catch (error) {
        console.error('Error uploading chunk:', error);
        return;
      } finally {
        loadingVideo = false;console.log("upload: " + loadingVideo); 
      }
    }

    alert('Video uploaded successfully');
    fetchVideos();
  }

  onMount(fetchVideos);

  async function viewVideo(videoId) {
    try {
      loadingVideo = true;
      selectedVideo = null;
      const res = await fetch(`https://allenskywolf.com/getVideoById?id=${videoId}`);
      if (!res.ok) throw new Error('Failed to fetch video');
      const videoBlob = await res.blob();
      selectedVideo = URL.createObjectURL(videoBlob);
    } catch (error) {
      console.error('Error fetching video:', error);
      alert('Failed to load video. Please try again.');
    } finally {
      loadingVideo = false;
    }
  }
</script>

<style>
  @import "tailwindcss";
</style>

<main class="min-h-screen bg-gradient-to-br from-blue-50 to-blue-200 flex flex-col items-center py-10">
  <div class="w-full max-w-2xl bg-white rounded-xl shadow-lg p-8">
    <h1 class="text-4xl font-extrabold text-blue-700 mb-8 text-center">Video Management</h1>

    <!-- Upload Form -->
    <form 
      on:submit|preventDefault={uploadVideo}
      class="flex flex-col gap-4 bg-blue-50 rounded-lg p-6 shadow mb-8"
    >
      <div class="flex flex-col gap-2">
        <label class="font-semibold text-blue-800">Video Name</label>
        <input 
          type="text" 
          bind:value={name} 
          placeholder="Enter video name" 
          required 
          class="input input-bordered border-blue-300 rounded px-3 py-2 focus:outline-none focus:ring-2 focus:ring-blue-400"
        />
      </div>
      <div class="flex flex-col gap-2">
        <label class="font-semibold text-blue-800">Category</label>
        <select 
          bind:value={category}
          class="input input-bordered border-blue-300 rounded px-3 py-2 focus:outline-none focus:ring-2 focus:ring-blue-400"
        >
          <option value="search_and_rescue">Search and Rescue</option>
          <option value="anadarko_safety">Anadarko Safety</option>
        </select>
      </div>
      <div class="flex flex-col gap-2">
        <label class="font-semibold text-blue-800">Video File</label>
        <input 
          type="file" 
          accept="video/mp4" 
          on:change={(e) => (video = e.target.files[0])} 
          required
          class="file:mr-4 file:py-2 file:px-4 file:rounded file:border-0 file:text-sm file:font-semibold file:bg-blue-100 file:text-blue-700"
        />
      </div>
      <button 
        type="submit"
        class="bg-blue-600 hover:bg-blue-700 text-white font-bold py-2 px-6 rounded transition"
      >
        Upload Video
      </button>
    </form>

    <!-- Uploaded Videos -->
    <h2 class="text-2xl font-bold text-blue-800 mb-4">Uploaded Videos</h2>
    <ul class="space-y-3 mb-8">
      {#each videos as video}
        <li class="flex items-center justify-between bg-blue-100 rounded p-3 shadow-sm">
          <div>
            <span class="font-semibold text-blue-900">{video.name}</span>
            <span class="ml-2 text-blue-600 text-sm">({video.category})</span>
          </div>
          <button 
            on:click={() => viewVideo(video.id)}
            class="bg-blue-500 hover:bg-blue-600 text-white px-4 py-1 rounded transition"
          >
            View
          </button>
        </li>
      {/each}
    </ul>

    <!-- Video Viewer -->
    {#if loadingVideo}
      <div class="flex flex-col items-center justify-center py-8">
        <svg class="animate-spin h-8 w-8 text-blue-600 mb-2" xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24">
          <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4"></circle>
          <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8v8z"></path>
        </svg>
        <p class="text-blue-700 font-semibold">Loading video...</p>
      </div>
    {/if}

    {#if selectedVideo}
      <div class="mt-8">
        <h2 class="text-xl font-bold text-blue-800 mb-2">Video Viewer</h2>
        <video
          controls
          autoplay
          playsinline
          muted
          width="600"
          class="rounded-lg border border-blue-300 shadow"
          style="max-width: 100%; height: auto;"
        >
          <source src={selectedVideo} type="video/mp4" />
          Your browser does not support the video tag.
        </video>
      </div>
    {/if}
  </div>
  <footer class="mt-10 text-blue-500 text-sm">
    Today: {new Date().getUTCMonth()}-{new Date().getDate()}-{new Date().getFullYear()} Created by: Ronald Kiefer
  </footer>
</main>