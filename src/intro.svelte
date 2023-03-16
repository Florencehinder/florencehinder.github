<script>
    import { readable, writable } from 'svelte/store';
  
    const introMessage = `Welcome! Please click the button below to start chatting with our AI assistant.`;
  
    export let startButtonLabel = 'Start Chatting';
    export let onComplete: (text: string) => void;
  
    const showButton = writable(false);
    const buttonText = readable(startButtonLabel);
  
    const startChatting = () => {
      showButton.set(false);
      onComplete('');
    };
  
    $: buttonText.set(startButtonLabel);
  
    setTimeout(() => {
      showButton.set(true);
    }, 1000);
  </script>
  
  <div class="intro-message">{introMessage}</div>
  {#if $showButton}
    <button on:click={startChatting}>{buttonText}</button>
  {/if}
  
  <style>
    .intro-message {
      margin-bottom: 20px;
    }
  </style>