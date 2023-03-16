<script lang="ts">
	import { browser } from '$app/environment';
	import { goto } from '$app/navigation';
	import { apiKeyIsSet, setApiKey } from '../main';
	import Chats from './chat/Chats.svelte';
	import Intro from './Intro.svelte';
	import TextInput from './TextInput.svelte';
	import { writable } from 'svelte/store';

	import chats from './stores';

	const src = 'background.svg';

	let requestApiKey = browser;
	let isChatting = false;
	let isFirstMessage = true;
	let questions = ["What do you enjoy learning about?", "What are your hobbies?", "Are you a spontaneous or routine person?", "Do you see yourself as someone who does manual labor or mental labor?", "Which of your skills are you most proud of?", "Do you plan to go to University?", "Is there any other information you would like to add?",];
	let currentQuestionIndex = 0;

	if (browser) {
		requestApiKey = !apiKeyIsSet();
		const urlParams = new URLSearchParams(window.location.search);
		const apiKey = urlParams.get('apiKey');
		if (apiKey) {
			setApiKey(apiKey);
			goto('./');
			requestApiKey = false;
		}
	}

	const onSubmitApiKey = (apiKey: string) => {
		setApiKey(apiKey);
		requestApiKey = false;
	};

	const startChatting = () => {
		isChatting = true;
		const newChats = [...$chats];
		const currentQuestion = questions[currentQuestionIndex];
		newChats.push({ content: currentQuestion, role: 'assistant' });
		currentQuestionIndex = (currentQuestionIndex + 1) % questions.length;
		chats.update(() => newChats);
	};

	const onEnterChatText = (text: string) => {
		const newChats = [...$chats];
		newChats.push({ content: text, role: 'user' });
		chats.update(() => newChats);
		if (isChatting) {
			const currentQuestion = questions[currentQuestionIndex];
			newChats.push({ content: currentQuestion, role: 'assistant' });
			currentQuestionIndex = (currentQuestionIndex + 1) % questions.length;
			chats.update(() => newChats);
		}
	};

	$:{ if ($chats.length === 0) { startChatting(); } }
</script>

<svelte:head>
	<title>Home</title>
	<meta name="description" content="Future Finder" />
</svelte:head>

{#if requestApiKey}
	<Intro />
{:else}
	<div class="flex-1 overflow-y-scroll" id="bg" style="background-image:url({src})">
		<div class="flex min-h-0">
			<Chats />
		</div>
	</div>
{/if}

<TextInput onComplete={requestApiKey ? onSubmitApiKey : onEnterChatText} />

<style>
	#bg {
		background-size: cover;
		background-position: center;
	}
</style>
