<template>
	<section>
		<aside>
			<div v-for="message in messages" :key="message.ID">
				<b>{{message.NAME}}:</b> &nbsp;					
				<span>{{message.TEXT}}</span>
			</div>
		</aside>
		<main>			
			<label for="name">Имя:</label>
			<input id="name" v-model="name">
			<label for="message">Сообщение:</label>
			<textarea id="message" v-model="message"></textarea>
			<br>
			<button v-if="name && message" @click="sendMessage">Send</button>			
		</main>
	</section>
</template>

<script setup>

import { ref, watch, onMounted } from 'vue'

let messages = ref([])
let name = ref('')
let message = ref('')

async function sendMessage() {	
	fetch('https://chatback.qcp.repl.co/add', {
    method: 'POST',
    headers: {
      'Accept': 'application/json',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({Name: name.value, Text: message.value})
  });
	
	message.value = ""
}

async function loadMessages() {
  let response = await fetch('https://chatback.qcp.repl.co/get')
	let msg = await response.json()
	messages.value = msg.sort((a, b) => new Date(a.TIME) < new Date(b.TIME) ? -1 : 1)
}

function scrollDown(){
	document.querySelector('aside').scrollTop = document.querySelector('aside').scrollHeight
}

onMounted(() => {
	loadMessages()
	setInterval(loadMessages, 2000)
	setTimeout(scrollDown, 500)
})

</script>

<style>
	section {
		display: flex;
		align-items: center;
		justify-content: space-around;
		height: 100vh;
		margin: 0;
	}
	main {
		width: 40%;
	}
	aside {
		border: 0.1rem solid #d1d1d1;
		border-radius: .4rem;
		padding: 0.5em;

		width: 50%;
		max-height: 80vh;

		overflow-y: scroll;
		overscroll-behavior-y: contain;
  	scroll-snap-type: y proximity;
	}
	aside > div:last-child {
		scroll-snap-align: end;
	}
	aside div {
		word-wrap: break-word;
	}
	#message {		
		resize: vertical;
		width: 100%;
	}
</style>