# Vue 2 Mailchimp Subscribe Component

Vue.js 2 component for ajax subscription to Mailchimp. Because of [CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS) limitations, it is hardly possible to do direct queries to Mailchimp API from Javascript in browser. It is not secure either, because you would show visitors your private API key.

This component goes around the limitations by doing a JSONP request to Mailchimp endpoint that normally handles Mailchimp-generated embed forms. As far as I know, this is the only way to do a pure front-end JS subscribe without requiring any back-end scripts on the server (like PHP to get users data and then do the call itself using Guzzle or cURL).  

## Usage

1. Set up your Vue.js instance and register the component as you would normally do

	```
	import Vue from 'vue';
	import Mailchimp from './js/components/Mailchimp.vue';
	
	new Vue({
	  el: '#app',
	  components: { Mailchimp },
	});
	```
	

2. Grab form post URL and validation code from Mailchimp:
	- Go to Mailchimp:	`Mailchimp acount > Lists > [choose list] > Signup forms > Embedded Forms > Super Slim`
	- Notice the HTML form in `Copy/paste onto your site` block
	- From the form, copy out two items: **Form action URL** (eg. *https://myaccount.us16.list-manage.com/subscribe/post?u=d993985c3acd21b37a3d9fef4&amp;id=2645adecd7*), and **Name of the last hidden input** (eg. *b_d992865c3acd21b37a3d9fef4_8515adecd7*).
	- In the form URL, replace the `&amp;` sign by regular `&`string


3. 	Call the component in your template, passing the two copied strings from step 2 in `action` and `validation` attributes. Eg.

```
<mailchimp
	action="https://myaccount.us16.list-manage.com/subscribe/post?u=d993985c3acd21b37a3d9fef4&id=2645adecd7"
	validation="b_d992865c3acd21b37a3d9fef4_8515adecd7"
></mailchimp>
```	
	
	
## Demo

The component was created for the [Whale Ride project](https://www.whaleride.co.nz/), so you can see it at the bottom of the page (unless it has already been replaced). If you used it for your project, I am happy to post the link here.

## Notes / To Do

- The AJAX request is done using jQuery. I tried using [AXIOS](https://github.com/axios/axios), but it didn't work very well, obviously they [don't support JSONP](https://github.com/axios/axios/issues/342), which is required in this case.
- There are some JSONP adapters for AXIOS [here](https://github.com/AdonisLau/axios-jsonp) and [here](https://github.com/RekingZhang/axios-jsonp), but I haven't got that far as testing them
