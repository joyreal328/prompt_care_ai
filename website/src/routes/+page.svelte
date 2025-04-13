<script>
    import { onMount } from 'svelte';
    import Microphone from '$lib/components/microphone.svelte';
  
    let showModal = false;
    let chatInput = "";
    let micIcon = "🎤"; // Using an emoji for the microphone icon
    let recognition;
    let speechSupported = false;


    let showMicWrapper = false;

    let showHeader = true;
    
  
    // Use onMount to ensure client-side execution (SSR protection)
    onMount(() => {
      if (typeof window !== 'undefined') {
        const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
        speechSupported = !!SpeechRecognition;
        if (speechSupported) {
          recognition = new SpeechRecognition();
          recognition.continuous = false;
          recognition.interimResults = false;
          recognition.lang = 'en-US';
  
          recognition.addEventListener('result', (event) => {
            // Set the recognized speech into our chat input
            chatInput = event.results[0][0].transcript;
            micIcon = "🎤"; // Reset the icon after capturing voice
          });
  
          recognition.addEventListener('error', (event) => {
            console.error('Speech recognition error:', event);
            micIcon = "🎤";
          });
        }
      }
    });
  
    function openChat() {
      showModal = true;
      showHeader = false;
    }
  
    function closeChat() {
      showModal = false;
    }
  
    function startSpeech() {
      if (recognition) {
        recognition.start();
        micIcon = "🎤..."; // Update icon to indicate recording
      }
    }
  </script>
  
 
  
  {#if showHeader}
  <div class="container">
    <h1>AI-Powered ER Triage</h1>
    <p>Cutting Wait Times, Saving Lives</p>
    <button class="chat-btn" on:click={openChat}>Talk to our Chatbot</button>
  </div>
  {/if}
  {#if showModal}

    <div class="chat-modal">
   
        <Microphone/>
    </div>
  {/if}



  

  <style>
    :global(body) {
      margin: 0;
      padding: 0;
      font-family: Arial, sans-serif;
      background: #f7f7f7;
    }
    .container {
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      height: 100vh;
      text-align: center;
    
    }
    h1 {
      font-size: 2.5rem;
      margin-bottom: 0.5rem;
    }
    p {
      font-size: 1.2rem;
      margin-bottom: 1.5rem;
    }
    .chat-btn {
      background: #007BFF;
      color: #fff;
      border: none;
      padding: 1rem 2rem;
      font-size: 1rem;
      border-radius: 5px;
      cursor: pointer;
    }
    .chat-modal {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(0, 0, 0, .3);
      display: flex;
      justify-content: center;
      align-items: center;
    }
    .chat-container {
      background: #000000;
      border-radius: 8px;
      padding: 20px;
      width: 90%;
      max-width: 400px;
      box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
    }
    .chat-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 10px;
    }
    .close-btn {
      cursor: pointer;
      border: none;
      background: transparent;
      font-size: 1.5rem;
    }
    .chat-body {
      border: 1px solid #ddd;
      padding: 10px;
      height: 300px;
      overflow-y: auto;
      margin-bottom: 10px;
    }
    .chat-input {
      display: flex;
      align-items: center;
    }
    .chat-input input[type="text"] {
      flex: 1;
      padding: 10px;
      border-radius: 5px;
      border: 1px solid #ccc;
      font-size: 1rem;
    }
    .mic-btn {
      background: none;
      border: none;
      cursor: pointer;
      margin-left: 10px;
      font-size: 1.5rem;
    }
  </style>