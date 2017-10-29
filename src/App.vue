<template>
	<div id="app">
		<h1 class="title centered-content">Messager</h1>
		<div class="messages">
			<div v-for="post in posts" v-bind:key="post.timestamp">
				<span class="timestamp">{{ post.timestamp }}</span>
				<div class="inline-children">
					<span class="user" v-if="post.user">{{ post.user }}:&nbsp;</span>
					<p class="message">{{ post.message }}</p>
				</div>
			</div>
		</div>
		<div class="input centered-content inline-children">
			<input v-model="message" v-on:keypress.enter="postMessage()" placeholder="New message" />
			<button v-on:click="postMessage()">POST</button>
		</div>
	</div>
</template>s

<script>
	'use strict';

	export default {
		name: "app",
		data() {
			return {
				db: null,
				users: [],
				user: '',
				posts: [],
				message: ""
			};
		},
		methods: {
			initFirebase() {
				this.db = firebase.database();
			},
			getPosts() {
				this.db.ref("/posts").on('value', posts => {
					this.posts = posts.val();
				});
			},
			postMessage() {
				if (this.message) {
					if (this.posts.length > 100) {
						this.posts.shift();
					}

					this.posts.push({
						message: this.message,
						timestamp: (new Date()).toString(),
						user: this.user
					});
					this.db.ref('/posts').set(this.posts);

					setTimeout(() => {
						const messages = document.querySelector('.messages');
						messages.scrollTo(0, messages.scrollHeight);
					}, 1);
				}

				document.querySelector('.input input').focus();
				this.message = '';
			}
		},
		// lifecycle
		mounted() {
			this.initFirebase();
			this.getPosts();
		}
	};
</script>

<style lang="scss">
	@import url('https://fonts.googleapis.com/css?family=Molle:400i');

	html {
		$background-color: whitesmoke;
		$color: #111;

		height: 100vh;
		font-size: 32px;

		* {
			position: relative;
			box-sizing: border-box;
			margin: 0 auto;
			padding: 0;
			color: $color;
			font-family: sans-serif;
			font-size: 1rem;
		}

		body {
			padding-top: 64px;
			height: 100%;
			background: radial-gradient($background-color, $color);
			overflow: hidden;

			*:not(script) {
				display: block; // border: 1px solid;
				h1 {
					font-size: 2rem;

					&.title {
						margin-bottom: 1rem;
						font-family: molle, cursive;
						font-size: 3rem;
						text-shadow: 0 2px 0 $background-color, 0 2px 3rem $background-color;
					}
				}

				input {
					padding: 0.25rem;
				}

				button {
					padding: 0.5rem 1rem;
					background-color: transparent;
					border: none;
					border-radius: 4px;
					cursor: pointer;
				}

				.centered-content {
					text-align: center;
				}

				.inline-children>* {
					display: inline-block;
				}

				$width: 75%;

				.messages {
					>div:not(:last-child) {
						margin-bottom: 1rem;
					}

					margin-bottom: 1.75rem;
					padding: 0.55rem;
					padding-bottom: 0.25rem;
					width: $width;
					height: 50%;
					max-height: 400px;
					background-color: $background-color;
					border: 8px solid;
					border-radius: 8px;
					box-shadow: 0 2px 16px $color;
					overflow-y: auto;

					.timestamp {
						color: lighten($color, 10%);
						font-size: 0.33rem;
					}

					.message {
						font-size: 1.1rem;
					}
				}

				.input {
					$box-shadow: 0 2px 16px $background-color;

					width: $width;

					input {
						font-size: 1rem;
						border: 1px solid;
						box-shadow: $box-shadow;
					}

					button {
						background-color: $color;
						color: $background-color;
						box-shadow: $box-shadow;
					}
				}
			}
		}
	}
</style>