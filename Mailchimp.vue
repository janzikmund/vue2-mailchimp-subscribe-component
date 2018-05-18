<template>
	<div class="mailchimp">
		<form class="mailchimp-subscribe" @submit.prevent="subscribe">
			<input v-model="email" type="email" name="EMAIL" value="" placeholder="YOUR EMAIL ADDRESS..." required>
			<input class="button" type="submit" value="Notify Me" :disabled="in_progress">
		</form>
		<div v-if="response" :class="['status', response_status]">{{ response }}</div>
	</div>
</template>

<script>
	import $ from 'jquery';
	const qs = require('qs');

	export default {
		props: {
			action: '',
			validation: '',
		},
		data: () => ({
			email: '',
			response: '',
			response_status: '',
			in_progress: false,
		}),
		methods: {
			subscribe: function (event) {
				this.in_progress = true;
				this.response = '';
				this.response_status = '';

				try{
				    $.ajax({
				        url: this.getTransformedAction(),
				        crossDomain: true,
				        data: this.prepareRequestData(),
				        method: "GET",
				        cache: false,
				        dataType: 'json',
				        contentType: 'application/json; charset=utf-8',
				        success: (data) => {
				        	// enable submission again
				        	this.in_progress = false;

				            // --- Success --
				            if (data.result === "success") {
				            	this.email = '';
				            	this.response = this.adjustResponse(data.msg);
				            	this.response_status = 'success';

				            // --- Error ---
				            } else {
				                // Error from Mail Chimp
				                this.response = this.adjustResponse(data.msg);
				                this.response_status = 'error';
				            }
				        }
				    });

				// request error
				} catch(err){
					console.log(err);
					this.in_progress = false;
					this.response = this.adjustResponse(err);
					this.response_status = 'error';
				}
			},

			// add few necessary params to default form action
			getTransformedAction() {
				var action = this.action.replace('/post?', '/post-json?') + "&c=?";
				return action;
			},

			// prepare data to pass to request
			prepareRequestData() {
				let data = {
					'EMAIL': this.email,
					'subscribe': 'Subscribe',
				};
				data[this.validation] = '';
				return qs.stringify( data );
			},

			// adjust response from mailchimp
			adjustResponse(text) {
				return text
					.replace(/<(?:.|\n)*?>/gm, '')						// strip html
					.replace('Click here to update your profile', ''); 	// remove update profile link
			}
		}
	}
</script>
