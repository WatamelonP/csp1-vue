<script setup>
  import { ref, onMounted, onBeforeMount } from 'vue';
  import { Notyf } from 'notyf';
  import 'notyf/notyf.min.css';

  const notyf = new Notyf();

  const WEB3FORM_ID = import.meta.env.VITE_WEB3FORM_ID;
  const subject = "New message from Porfolio Contact Form"

   const name = ref("");
   const email = ref("");
   const message = ref("");

   const isLoading = ref(false);

   async function handleSubmit() {
    
    if(!recaptchaToken.value) {  // ← add this check
    notyf.error("Please complete the reCAPTCHA.");
    return;
    }

    isLoading.value = true;

    try{
      const response = await fetch("https://api.web3forms.com/submit", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
          Accept: "application/json"
        },
        body: JSON.stringify({
          access_key: WEB3FORM_ID,
          subject: subject,
          name: name.value,
          email: email.value,
          message: message.value
        })
      });
      const result = await response.json();

      if(result.success) {
        console.log(result)

        isLoading.value = false;
        notyf.success("Message sent successfully!");
      }
    }
    catch(error) {
      console.log(error);
      isLoading.value = false;
      notyf.error("An error occurred. Please try again.");
    } finally {
      resetRecaptcha();
    }
   }

   const SITE_KEY = import.meta.env.VITE_RECAPTCHA_SITE_KEY;


	const recaptchaContainer = ref(null);
	const recaptchaWidgetId = ref(null);
	const recaptchaToken = ref('');


	function onRecaptchaSuccess(token) {
		recaptchaToken.value = token;
	}

	function onRecaptchaExpired() {
		recaptchaToken.value = '';
	}

	function renderRecaptcha() {
		if(!window.grecaptcha) {
			console.error('reCAPTCHA not loaded');
			return;
		}

		recaptchaWidgetId.value = window.grecaptcha.render(recaptchaContainer.value, {
			sitekey: SITE_KEY,
			size: 'normal',
			callback: onRecaptchaSuccess,
			'expired-callback': onRecaptchaExpired
		});
	}

	function resetRecaptcha() {
		if(recaptchaWidgetId.value !== null) {
			window.grecaptcha.reset(recaptchaWidgetId.value);
			recaptchaToken.value = '';
		}
	}

	onMounted(() => {
		const interval = setInterval(() => {
			if(window.grecaptcha && window.grecaptcha.render) {
				renderRecaptcha();
				clearInterval(interval)
			}
		}, 100);

		onBeforeMount(() => {
			clearInterval(interval);
		});
	})
</script>

<template>
  <!-- CONTACT -->
  <section id="contact">
    <div class="container">
      <div class="row gy-5 align-items-center">
        <div class="col-md-5">
          <h2 class="contact-cta">
            <span class="contact-line-1">WANT TO</span>
            <span class="contact-line-2">GET IN</span>
            <span class="contact-line-3">TOUCH?</span>
          </h2>
          <p class="contact-tagline">Feel free to message me on any of my socials</p>
          <ul class="social-links">
            <li><a href="https://www.facebook.com/marlon.vincent.5" target="_blank" rel="noopener noreferrer">Facebook</a></li>
            <li><a href="https://www.linkedin.com/in/3b5981352/" target="_blank" rel="noopener noreferrer">LinkedIn</a></li>
            <li><a href="https://discord.com/users/296123775970181131" target="_blank" rel="noopener noreferrer">Discord</a></li>
            <li><a href="https://github.com/WatamelonP" target="_blank" rel="noopener noreferrer">Github</a></li>
          </ul>
        </div>
        <div class="col-md-7">
          <form @submit.prevent="handleSubmit">
            <div class="contact-form-card">
              <h4>Send a Message</h4>
              <div class="mb-3">
                <label class="form-label" for="contactName">Name</label>
                <input type="text" v-model="name" class="form-control" id="contactName" placeholder="Name" />
              </div>
              <div class="mb-3">
                <label class="form-label" for="contactEmail">Email</label>
                <input type="email" v-model="email" class="form-control" id="contactEmail" placeholder="Email" />
              </div>
              <div class="mb-3">
                <label class="form-label" for="contactMessage">Message</label>
                <textarea class="form-control" v-model="message" id="contactMessage" rows="4" placeholder="Message"></textarea>
              </div>
              <button class="btn-submit" :disabled="isLoading">{{ isLoading ? 'Sending...' : 'Submit' }}</button>
            </div>
             <div class="d-flex justify-content-end mt-2">
	                            	<div ref="recaptchaContainer"></div>
	          </div>
          </form>
        </div>
      </div>
    </div>
  </section>
</template>
