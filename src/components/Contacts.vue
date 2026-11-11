<script setup>
    import { ref, onMounted, onBeforeUnmount} from 'vue';
    import axios from 'axios';
    import { Notyf } from 'notyf';
    import 'notyf/notyf.min.css';

    const notyf = new Notyf();

    const WEB3FORMS_ACCESS_KEY = 'f965fc96-ebbe-4f22-9750-fd42dc8f25b8';
    const SITE_KEY = '6LetlhouAAAAANG9n2HNhLWs33oxIZd_IhP5o1jc'; // Replace with your Vercel site key

    // Initial value for the email:

    const subject = "New message from Portfolio Contact Form";
    const name = ref("");
    const email = ref("");
    const message = ref("");

    const isLoading = ref(false);

    // Function for sending our email:

    const submitForm = async () => {

        if (!recaptchaToken.value) {
            notyf.error('Please verify that you are not a robot.');
            return;
        }

        isLoading.value = true;

        try{
            const response = await axios.post("https://api.web3forms.com/submit",{
                access_key: WEB3FORMS_ACCESS_KEY,
                subject: subject,
                name: name.value,
                email: email.value,
                message: message.value
            },{
                headers:{
                    'Content-Type': "application/json",
                    Accept:"application/json"
                }
            });

            const result = response.data;
            if(result.success){
                notyf.success("Your message has been sent successfully!");
                name.value = "";
                email.value = "";
                message.value = "";
                resetRecaptcha(); // Automatically clears/resets the reCAPTCHA widget
            }else{
                notyf.error(response.data.message || "Failed to send message. Please try again.");
            }

        }catch(error){
            notyf.error(error.response?.data?.message || "An error occurred while sending your message.");
        }finally{
            isLoading.value = false;
        }
    }

    const recaptchaContainer = ref(null);
    const recaptchaWidgetId = ref(null);
    const recaptchaToken = ref('');

    // Callback called by reCAPTCHA when successful
    function onRecaptchaSuccess(token) {
        recaptchaToken.value = token;
    }

    // Callback when expired
    function onRecaptchaExpired() {
        recaptchaToken.value = '';
    }

    // Function to render the reCAPTCHA widget
    function renderRecaptcha() {
        if (!window.grecaptcha) {
            console.error('reCAPTCHA not loaded');
            return;
        }

        recaptchaWidgetId.value = window.grecaptcha.render(recaptchaContainer.value, {
            sitekey: SITE_KEY,
            size: 'normal',
            callback: onRecaptchaSuccess,
            'expired-callback': onRecaptchaExpired,
        });
    }

    // Function to reset reCAPTCHA 
    function resetRecaptcha() {
        if (recaptchaWidgetId.value !== null) {
            window.grecaptcha.reset(recaptchaWidgetId.value);
            recaptchaToken.value = '';
        }
    }

    onMounted(() => {
        const interval = setInterval(() => {
            if (window.grecaptcha && window.grecaptcha.render) {
                renderRecaptcha();
                clearInterval(interval);
            }
        }, 100);

        onBeforeUnmount(() => {
            clearInterval(interval);
        });
    });
</script>

<template>
    <!-- contact -->
    <h1 class="text-center my-4 pt-5" id="contact">Contact</h1>
    <div class="contact-section">
        <div class="row align-items-center mt-4">
            <div class="col-md-6 map-container">
                <iframe id="gmap_canvas" src="https://maps.google.com/maps?q=centro%20escolar%20university%20manila&t=&z=13&ie=UTF8&iwloc=&output=embed" frameborder="0" scrolling="no" marginheight="0" marginwidth="0"></iframe>
            </div>
            <div class="col-md-6">
                <form @submit.prevent="submitForm">
                    <div class="mb-3">
                        <input 
                            v-model="name" 
                            type="text" 
                            class="form-control contact-form-control" 
                            placeholder="First Name M.I. Last Name"
                        >
                    </div>
                    <div class="mb-3">
                        <input 
                            v-model="email" 
                            type="email" 
                            class="form-control contact-form-control" 
                            placeholder="Email"
                        >
                    </div>
                    <div class="mb-3">
                        <textarea 
                            v-model="message" 
                            class="form-control contact-form-control" 
                            rows="6" 
                            placeholder="Message">
                        </textarea>
                    </div>
                    <div class="form-footer">
                        <div class="social-icons">
                            <a href="https://www.linkedin.com/in/charles-babbage-8291a6211/" id="linkedin"><i class="fab fa-linkedin"></i></a>
                            <a href="https://gitlab.com/cbabbage0991" id="gitlab"><i class="fab fa-gitlab"></i></a>
                            <a href="https://github.com/cbabbage0991" id="github"><i class="fab fa-github"></i></a>
                        </div>
                        <button type="submit" class="submit-btn pl-5 pr-5" :disabled="isLoading">
                            {{ isLoading ? 'Sending...' : 'Submit' }}
                        </button>
                    </div>

                    <!-- recaptcha checkbox -->
                    <div class="d-flex justify-content-end mt-2">
                        <div ref="recaptchaContainer"></div>
                    </div>
                </form>
            </div>
        </div>
    </div>
</template>