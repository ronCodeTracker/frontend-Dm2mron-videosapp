
<script>
  import { onMount } from 'svelte';
  let videos = [];
  let name = '';
  let category = 'search_and_rescue';
  let video;

  let selectedVideo = null; // To store the selected video for viewing
  let loadingVideo = false; // <-- Add



  const API_BASE_URL = 'https://allenskywolf.com';


  // Fetch videos from the backend
  async function fetchVideos() {
    try {
      const res = await fetch('https://allenskywolf.com/getAllVideos');
      if (!res.ok) throw new Error('Failed to fetch videos');
      videos = await res.json();
    } catch (error) {
      console.error('Error fetching videos:', error);
    }
  }

  // Upload video in chunks
  async function uploadVideo() {
    const CHUNK_SIZE = 5 * 1024 * 1024; // 5MB
    const totalChunks = Math.ceil(video.size / CHUNK_SIZE);

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
      }
    }

    alert('Video uploaded successfully');
    fetchVideos();
  }

  // Fetch videos on component mount
  onMount(fetchVideos);

  // Function to select a video for viewing
  async function viewVideo(videoId) {
  try {
    loadingVideo = true; // <-- Start loading
    console.log("videoId = ", videoId);
    
    const res = await fetch(`https://allenskywolf.com/getVideoById?id=${videoId}`);
    if (!res.ok) throw new Error('Failed to fetch video');

    // Convert the response into a Blob
    const videoBlob = await res.blob();

    // Create an object URL for the video Blob
    selectedVideo = URL.createObjectURL(videoBlob);
  } catch (error) {
    console.error('Error fetching video:', error);
    alert('Failed to load video. Please try again.');
  } finally {
    loadingVideo = false; // <-- Stop loading
  }
}


</script>

<style>
    @import "tailwindcss";      


</style>


<main>
  <h1>Video Management</h1>

  <!-- Upload Form -->
  <form on:submit|preventDefault={uploadVideo}>
    <input type="text" bind:value={name} placeholder="Video Name" required />
    <select bind:value={category}>
      <option value="search_and_rescue">Search and Rescue</option>
      <option value="anadarko_safety">Anadarko Safety</option>
    </select>
    <input type="file" accept="video/mp4" on:change={(e) => (video = e.target.files[0])} required />
    <button type="submit">Upload Video</button>
  </form>

  <!-- Uploaded Videos -->
  <h2>Uploaded Videos</h2>
  <ul>
    {#each videos as video}
      <li>
        {video.name} ({video.category})
        <button on:click={() => viewVideo(video.id)}>View</button>
      </li>
    {/each}
  </ul>
  <!-- Video Viewer -->

  {#if loadingVideo}
    <p>Loading video...</p>
  {/if}


  {#if selectedVideo}
    <h2>Video Viewer</h2>
    <video
      controls
      autoplay
      playsinline
      muted
      width="600"
      style="max-width: 100%; height: auto;"
    >
      <source src={selectedVideo} type="video/mp4" />
      Your browser does not support the video tag.
    </video>
  {/if}

<h3 class="text-3xl font-bold underline">
    Hello world!
  </h3>

</main>
