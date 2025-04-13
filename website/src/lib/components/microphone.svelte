<script>
    import { goto } from '$app/navigation';
    import { GoogleGenAI } from "@google/genai";
  
    console.log("Gemini key is:", import.meta.env.VITE_GEMINI_API_KEY);
    let apiKey = import.meta.env.VITE_GEMINI_API_KEY;
  
    // Declare the global flag to control chatbot speech
    let talkBackEnabled = true;
  
    // Create an instance of the GoogleGenAI client with your API key.
    const ai = new GoogleGenAI({ apiKey: apiKey });
  
    let showMicWrapper = true;
    let statusText = "Click the microphone button to start.";
    let transcript = "";
    let recognition = null;
  
    // Function to send user message using GoogleGenAI and speak the reply
    async function sendChatMessage() {
      if (transcript.trim() === "") {
        statusText = "No message to send.";
        return;
      }
      
      // Save the user's message then clear transcript for new input
      const userMessage = transcript.trim();
      transcript = "";
      statusText = "Sending...";
      
      try {
        // Use the GoogleGenAI library to generate content
        const response = await ai.models.generateContent({
          model: "gemini-2.0-flash",
          contents: userMessage
        });
        
        // Retrieve the reply from the response
        const botReply = response.text || "No reply from Gemini API.";
        statusText = "Reply received.";
        
        console.log(botReply);
        speak(botReply);
        
      } catch (error) {
        console.error("Error sending chat message:", error);
        statusText = "Error sending message.";
      }
    }
    
    // Modified speak function that checks the talkBackEnabled flag
    function speak(text) {
      if (!talkBackEnabled) {
        console.log("Talk back is disabled.");
        return;
      }
      
      const utterance = new SpeechSynthesisUtterance(text);
      window.speechSynthesis.speak(utterance);
    }
    
    // Function to override and stop talking back immediately
    function overrideStopTalkingBack() {
      // Disable any future speech responses
      talkBackEnabled = false;
      
      // Cancel any ongoing speech synthesis
      window.speechSynthesis.cancel();
      
      console.log("Override: Chatbot speech has been stopped.");
    }
    
    // Function to initialize and start speech recognition
    function startMic() {
      try {
        const SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
        if (!SpeechRecognition) {
          statusText = "Speech recognition is not supported in your browser.";
          return;
        }
        recognition = new SpeechRecognition();
        recognition.continuous = true;
        recognition.interimResults = false;
    
        recognition.onstart = () => {
          statusText = "Listening...";
        };
    
        recognition.onresult = (event) => {
          let finalTranscript = "";
          for (let i = event.resultIndex; i < event.results.length; i++) {
            if (event.results[i].isFinal) {
              finalTranscript += event.results[i][0].transcript + " ";
            }
          }
          transcript += finalTranscript;
        };
    
        recognition.onerror = (event) => {
          console.error("Speech recognition error:", event.error);
          statusText = "Error: " + event.error;
        };
    
        recognition.start();
      } catch (error) {
        console.error("Error initializing speech recognition:", error);
        statusText = "Error accessing speech recognition.";
      }
    }
    
    // Function to stop speech recognition without leaving the page
    function stopMic() {
      if (recognition) {
        recognition.stop();
        recognition = null;
        statusText = "Mic stopped.";
      } else {
        statusText = "Mic is not running.";
      }
    }
    
    // Function to close the microphone and navigate away
    function closeMic() {
      showMicWrapper = false;
      if (recognition) {
        recognition.stop();
        recognition = null;
      }
      statusText = "Mic stopped.";
      goto('/');
      window.location.reload();
    }
    
    async function main() {
      const response = await ai.models.generateContent({
        model: "gemini-2.0-flash",
        contents: transcript
      });
      console.log(response.text);
    }
    
    main();
  </script>
  
  {#if showMicWrapper}
    <!-- Close Button at the Top Right -->
    <button class="close-button" aria-label="Close" on:click={closeMic}>X</button>
    
    <div class="loading-screen">
      <div class="loading-circle"></div>
      <div class="action-buttons">
        <button class="mic-button" aria-label="Start Microphone" on:click={startMic}>🎤</button>
        <button class="mic-button" aria-label="Stop Microphone" on:click={stopMic}>
          <!-- Stop Mic Icon -->
          <svg viewBox="0 0 24 24" width="24" height="24" fill="none" xmlns="http://www.w3.org/2000/svg">
            <!-- Microphone Icon -->
            <path
              d="M12 15a3 3 0 0 0 3-3V6a3 3 0 0 0-6 0v6a3 3 0 0 0 3 3z"
              stroke="black"
              stroke-width="2"
              stroke-linecap="round"
              stroke-linejoin="round"
            />
            <path
              d="M5 10a7 7 0 0 0 14 0"
              stroke="black"
              stroke-width="2"
              stroke-linecap="round"
              stroke-linejoin="round"
            />
            <!-- Stop Overlay Icon -->
            <rect x="8" y="8" width="8" height="8" fill="red" opacity="0.7"/>
          </svg>
        </button>
        <button class="btn-send" aria-label="Send Message" on:click={sendChatMessage}>SEND</button>
      </div>
     
      <p>{statusText}</p>
      <p>{transcript}</p>
    </div>
    
    <!-- Emergency Stop Button at the Very Bottom -->
    <button class="emergency-stop" on:click={overrideStopTalkingBack}>EMERGENCY STOP</button>
  {/if}
  
  <style>
    :global(html, body) {
      margin: 0;
      padding: 0;
      height: 100%;
      font-family: sans-serif;
    }


    .btn-send {
        background-color: green;
        border:none;
        color: white;
    }
    
    /* Close button positioned at the top right */
    .close-button {
      position: fixed;
      top: 10px;
      right: 10px;
      background: #fff;
      border: 2px solid #ccc;
      border-radius: 50%;
      width: 50px;
      height: 50px;
      font-size: 20px;
      cursor: pointer;
      outline: none;
    }
    
    .close-button:hover {
      border-color: #1e90ff;
    }
    
    /* Emergency Stop button positioned at the bottom center */
    .emergency-stop {
      position: fixed;
      bottom: 10px;
      left: 50%;
      transform: translateX(-50%);
      padding: 10px 20px;
      background: #fff;
      border: 2px solid #ccc;
      border-radius: 5px;
      cursor: pointer;
      outline: none;
    }
    
    .emergency-stop:hover {
      border-color: #1e90ff;
    }
    
    .loading-screen {
      position: relative;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      height: 90vh;
      width: 100%;
    }
    
    .loading-circle {
      width: 200px;
      height: 200px;
      border-radius: 50%;
      background: radial-gradient(
        circle at center,
        #d4f0ff,
        #87cefa,
        #1e90ff
      );
      box-shadow: 0 0 20px rgba(30, 144, 255, 0.3);
      animation: pulse 2s infinite;
    }
    
    @keyframes pulse {
      0% {
        transform: scale(1);
        box-shadow: 0 0 20px rgba(30, 144, 255, 0.3);
      }
      50% {
        transform: scale(1.05);
        box-shadow: 0 0 40px rgba(30, 144, 255, 0.5);
      }
      100% {
        transform: scale(1);
        box-shadow: 0 0 20px rgba(30, 144, 255, 0.3);
      }
    }
    
    .action-buttons {
      position: absolute;
      bottom: 50px;
      display: flex;
      gap: 10px;
    }
    
    .mic-button {
      background: #fff;
      border: 2px solid #ccc;
      border-radius: 50%;
      width: 50px;
      height: 50px;
      font-size: 20px;
      cursor: pointer;
      outline: none;
      display: flex;
      align-items: center;
      justify-content: center;
    }
    
    .mic-button:hover {
      border-color: #1e90ff;
    }
    
    p {
      margin-top: 20px;
      font-size: 1rem;
      color: #333;
    }
  </style>
  