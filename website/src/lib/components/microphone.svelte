<script>
    // You can add any necessary logic here if needed
  </script>
  
  <!-- Loading Screen Container -->
  <div class="loading-screen">
    <!-- Circle Element -->
    <div class="loading-circle"></div>
  
    <!-- Optional: Action Buttons -->
    <div class="action-buttons">
      <button class="mic-button" aria-label="Microphone">🎤</button>
      <button class="close-button" aria-label="Close">✕</button>
    </div>
  </div>
  
  <style>
    /* Reset styles for the component */
    :global(html, body) {
      margin: 0;
      padding: 0;
      height: 100%;
      /* background-color: #ffffff; */
      font-family: sans-serif;    
    }
  
    /* Full-page loading screen container */
    .loading-screen {
      position: relative;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      height: 100vh;
      width: 100vw;
    }
  
    /* Centered circle with a radial gradient */
    .loading-circle {
      width: 200px;
      height: 200px;
      border-radius: 50%;
      background: radial-gradient(
        circle at center,
        #d4f0ff, /* Lighter blue at the center */
        #87cefa, /* Transition color */
        #1e90ff  /* Darker blue at the edges */
      );
      box-shadow: 0 0 20px rgba(30, 144, 255, 0.3);
      animation: pulse 2s infinite;
    }
  
    /* Pulsing effect animation */
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
  
    /* Optional: Positioning for action buttons */
    .action-buttons {
      position: absolute;
      bottom: 50px;
      display: flex;
      gap: 10px;
    }
  
    /* Styling for the microphone and close buttons */
    .mic-button,
    .close-button {
      background: #fff;
      border: 2px solid #ccc;
      border-radius: 50%;
      width: 50px;
      height: 50px;
      font-size: 20px;
      cursor: pointer;
      outline: none;
      border:1px solid red;
    }
  
    /* Hover effects for buttons */
    .mic-button:hover,
    .close-button:hover {
      border-color: #1e90ff;
    }
  </style>
  